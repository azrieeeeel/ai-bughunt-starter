---
name: vuln-discovery-harness
description: Lapisan orkestrasi di luar model. Mengatur pipeline 8 tahap,
  menyimpan state agar bisa resume, dan memverifikasi temuan lewat gate
  yang tugasnya membantah, bukan mencatat temuan sendiri.
---

# Harness: vuln-discovery-harness

Harness ini adalah yang membedakan hasil terverifikasi dari AI slop.
Modelnya bisa diganti kapan saja; harness-nya yang bertahan.

## Pipeline (8 tahap)
```
recon -> hunt -> validate -> gapfill -> dedupe -> trace -> feedback -> report
```
- recon    : jalankan agent recon, hasil ke surface.json
- hunt     : webapp-hunter + skill, boleh paralel per skill
- validate : VERIFICATION GATE (lihat bawah) — ini intinya
- gapfill  : isi bukti yang kurang untuk kandidat borderline
- dedupe   : gabungkan kandidat duplikat lintas skill
- trace    : rekonstruksi langkah reproduksi dari log
- feedback : kandidat lemah balik ke hunt dengan catatan
- report   : render pakai templates/report-template.md

## Eksekusi paralel
Tahap hunt boleh menjalankan beberapa agent sekaligus, satu per skill
(idor-api, authz-matrix, ssrf-probe, xss-web). Masing-masing punya
budget request sendiri. File-upload dan graphql-introspect antre.

## State dan checkpoint
- Checkpoint ke `sqlite(run_id, stage)` setelah tiap tahap.
- Resume dari checkpoint terakhir tanpa mengulang tahap yang selesai.
- Pada transient error (429, timeout), retry dengan backoff lalu resume.
- Findings di-stream ke disk begitu ada, bukan ditahan di memori.

## VERIFICATION GATE — bagian terpenting
Gate dijalankan oleh model/instance TERPISAH dari yang memproduksi
temuan. Tugas gate adalah MEMBANTAH, bukan menyetujui.

Untuk tiap kandidat, gate menolak kecuali SEMUA ini terpenuhi:
1. Ada request mentah yang bisa diputar ulang.
2. Ada bukti dampak nyata, bukan sekadar beda respons:
   - IDOR  : read-back data akun lain cocok dengan baseline.
   - XSS   : eksekusi JS terkonfirmasi di browser / callback OOB.
   - SSRF  : callback OOB diterima di interactsh milik sendiri.
3. Target request ada di dalam scope (cek ulang terhadap engagement).
4. Bukan duplikat dari kandidat yang sudah confirmed.

Output gate:
```
candidates N  ->  confirmed X  |  rejected Y
```
Alasan reject dicatat eksplisit, contoh: `no_proof, cuma refleksi respons`.
Hanya kandidat confirmed yang boleh masuk tahap report.

## Stop condition dan budget
- stop: 3 confirmed  ATAU  800 request  ATAU  60 menit
- budget biaya: cap mis. 8.00 USD, hard stop saat tercapai
- saat salah satu tercapai, checkpoint lalu berhenti bersih

## Prinsip
Kalau tidak ada gate, agent akan mengirim semua kandidat sebagai "bug".
Gate = evidence dicek sebelum submit. Itu garis antara signal dan slop.
