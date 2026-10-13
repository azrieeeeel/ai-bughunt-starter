# Engagement Rules — ISI DULU SEBELUM MENJALANKAN APAPUN

> File ini dimuat oleh SEMUA agent sebelum request pertama. Kalau scope
> salah di sini, agent akan bekerja di luar izin. Perlakukan ini sebagai
> satu-satunya sumber kebenaran soal apa yang boleh disentuh.

## Program
- Nama program     : <isi, misal: Elastic on HackerOne>
- Platform         : <HackerOne / Bugcrowd / Intigriti / YesWeHack / private>
- URL policy/scope : <link halaman scope resmi>
- Tanggal cek scope: <YYYY-MM-DD>   # scope berubah, cek ulang tiap mulai

## In-scope (HANYA ini yang boleh disentuh)
# Satu per baris. Pakai pola eksplisit, jangan wildcard liar.
- *.target.example
- api.target.example
- cloud.target.example

## Out-of-scope (JANGAN disentuh, walau menggoda)
- Subdomain pihak ketiga yang cuma di-CNAME ke target
- <staging/internal yang tidak tercantum in-scope>
- <aset vendor, email, karyawan>

## Vuln class yang jadi fokus
- Broken Access Control (IDOR / BOLA)
- Business Logic Error
- XSS (reflected / DOM)
- Sisa OWASP Top 10 yang relevan

## Akun uji (test credentials untuk skenario attacker/victim)
# JANGAN pakai kredensial asli. Buat akun khusus untuk pengujian.
- Attacker : <email+attacker@...>  /  <password>
- Victim   : <email+victim@...>   /  <password>
- Catatan  : dua akun pada TENANT yang sama untuk uji IDOR cross-user

## Batas operasi (hard limits)
- Rate limit probing   : maks 5 req/detik
- Rate limit crawling  : maks 2 req/detik
- Backoff              : eksponensial saat kena HTTP 429
- Budget waktu per run : 60 menit
- Budget request       : 800 request
- Stop condition       : 3 temuan terkonfirmasi ATAU budget habis

## Aturan mutlak
- DILARANG uji di endpoint destruktif (hapus massal, transfer dana, dsb)
  tanpa izin eksplisit dan akun sandbox.
- DILARANG mass enumeration / data exfiltration dari data user asli.
- DILARANG menyentuh data user asli — hanya object milik akun uji sendiri.
- Kalau ragu suatu aset in-scope atau tidak: anggap OUT, lewati, catat.
