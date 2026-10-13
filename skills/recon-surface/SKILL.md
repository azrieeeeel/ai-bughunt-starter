---
name: recon-surface
description: Enumerasi dan klasifikasi permukaan serang untuk target in-scope.
  Trigger di awal engagement atau saat scope program berubah. Mengubah aset
  mentah jadi daftar endpoint terklasifikasi yang bisa dipakai hunter.
  Tidak melakukan pengujian vuln sama sekali.
tools: [subfinder, dnsx, httpx, katana, gau, jq]
severity_hint: none
---

## Preconditions
- `engagement-rules.md` sudah dibaca dan scope sudah divalidasi.
- Tujuan tahap ini: memetakan, BUKAN menguji. Tidak ada payload.

## Urutan kerja
1. Passive dulu (murah, tidak menyentuh target):
   - `subfinder`, `gau`, certificate transparency.
2. Resolve dan probe status:
   - `dnsx` untuk host yang hidup, `httpx` untuk status/title/tech.
3. Dedup dan normalisasi:
   - samakan skema, buang port default ganda, gabungkan host dengan
     respons identik.
4. Crawl terbatas:
   - `katana` kedalaman 3, aktifkan JS crawl untuk endpoint front-end.

## Klasifikasi output (sinyal untuk hunter)
Tandai tiap endpoint dengan sinyal yang mengarah ke skill tertentu:
- object_id di path/query/body/header  -> kandidat `idor-api`
- input yang terefleksi ke respons HTML -> kandidat `xss-web`
- param berisi URL/webhook/image-fetch  -> kandidat `ssrf-probe`
- endpoint upload / parser file         -> kandidat `file-upload`
- perbedaan respons antar role/tenant   -> kandidat `authz-matrix`

## Output format
Daftar JSON per endpoint: {url, method, params, tech, signals[]}.
Simpan ke `recon/surface.json`. Hunter membaca file ini, bukan output chat.

## Aturan mutlak
- Dilarang kirim payload, memodifikasi parameter untuk memicu error,
  atau menguji authz. Kalau menemukan sesuatu yang mencurigakan: catat
  sebagai sinyal dan lanjut. Verifikasi bukan pekerjaan skill ini.
- Aset out-of-scope dibuang di tahap normalisasi, bukan di tahap probing.
- Wildcard scope diperluas hanya sampai kedalaman yang diizinkan program.
  Jangan menyentuh subdomain pihak ketiga yang cuma di-CNAME.
