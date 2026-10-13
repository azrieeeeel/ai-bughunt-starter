Title:     <Vulnerability type> - <short impact-focused description> (<0-click / 1-click / auth required>)
Scope:     <target URL / package / endpoint> (<component@version>)
Weakness:  <Weakness name> (CWE-XXX)
Severity:  <None / Low / Medium / High / Critical>
Link:
Date:      YYYY-MM-DD
By:        @<username>
CVE IDs:
Details:

## Summary & Details
This vulnerability occurs when <what the app does wrong>. It lives in
<file / endpoint / component>, where <root cause in plain words>. An
attacker <does this simple thing> and <this happens>. Affected versions:
<version range>.

## Expected Result
<What the app should do instead, in one sentence.>

## Actual Result
<What really happens, with the evidence you saw: error text, timing,
log line, or screenshot.>

## Impact
<Who gets hurt, what the attacker gets, and what they need first
(an account, a link click, nothing at all).>

## Steps to Reproduce
Lab setup: <accounts, versions, and anything you set up first.>

1. Log in to <URL> as <user> / <password>.
2. <Next action, verb first.>
3. <Final step that shows the bug.>

The underlying request:

```http
<METHOD> /<path> HTTP/1.1
Host: <host>
Cookie: <session>

<body / payload>
```

---
# Aturan penulisan (anti AI slop) — hapus bagian ini sebelum submit
# - Ketik sendiri dulu poin utamanya, baru minta AI kembangkan.
# - Tiap section = satu paragraf singkat.
# - Bahasa simpel, jangan terlalu kompleks.
# - Jujur soal dampak, jangan dilebay-lebaykan.
# - Jangan pakai em-dash dan karakter khas AI lainnya.
# - Kalau report Bahasa Indonesia, JANGAN translate istilah teknis.
# - Pakai template yang sama tiap report biar konsisten.
# - Suruh AI jadi triager dulu: minta dia cari celah buat menolak
#   report-mu. Perbaiki dulu, baru submit.
