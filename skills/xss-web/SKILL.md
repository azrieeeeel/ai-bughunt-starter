---
name: xss-web
description: Deteksi reflected dan DOM-based XSS pada web app. Trigger saat
  input user muncul kembali di respons HTML, di atribut, di konteks JS,
  atau mengalir ke sink DOM berbahaya.
tools: [http, headless-chrome, katana, jsluice, interactsh]
severity_hint: P3-P2
---

## Preconditions
- Headless browser aktif, karena reflection bukan bukti eksekusi.
- Canary unik per request, format `zq7x` + 4 digit random.
- CSP header target sudah dicatat sebelum pengujian dimulai.

## Attack surface
- Query dan body param yang direfleksikan di respons.
- Path segment yang masuk ke breadcrumb atau error page.
- Header yang dirender: `Referer`, `User-Agent`, `X-Forwarded-Host`.
- Fragment `#` untuk DOM XSS, tidak pernah sampai ke server.
- Sink DOM dari hasil `jsluice`: `innerHTML`, `document.write`,
  `eval`, `setTimeout` string, `location`, `jQuery.html()`.
- Source: `location.hash`, `location.search`, `postMessage`,
  `localStorage`, `document.referrer`.

## Context detection
Kirim canary polos dulu, lalu tentukan konteks refleksinya:
- HTML body      -> butuh `<` dan `>` hidup
- Atribut quoted -> butuh `"` atau `'` untuk break out
- Atribut event  -> sudah dalam konteks JS, cukup delimiter
- JS string      -> butuh `'` `"` atau backtick, cek escaping `\`
- URL attribute  -> cek skema yang diizinkan
- Template engine-> cek ekspresi `{{ }}` sebelum ke payload HTML

## Verifikasi (apa yang bikin ini BUKTI)
- Positif = eksekusi JS benar-benar terjadi di headless browser
  (callback ke interactsh atау `alert`/`document.title` berubah).
- Refleksi tanpa eksekusi = `no_proof`. Jangan laporkan sebagai XSS.
- Simpan: URL/request pemicu, konteks, screenshot/DOM saat eksekusi.

## Aturan mutlak
- Payload non-destruktif. Dilarang `document.cookie` exfil ke host
  pihak ketiga selain canary interactsh milik sendiri.
- Jangan uji di form yang mengirim notifikasi/email ke user asli.
