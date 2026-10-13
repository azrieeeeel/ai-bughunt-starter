---
name: idor-api
description: Deteksi IDOR / BOLA pada REST dan GraphQL API. Trigger saat
  endpoint membawa object identifier di path, query, body, atau header,
  dan tersedia dua akun uji pada tenant yang sama.
tools: [http, burp-autorize, ffuf, jq, interactsh]
severity_hint: P2-P1
---

## Preconditions
- 2 session valid: `SESS_A` (owner), `SESS_B` (attacker), tenant sama.
- Canary object dibuat dari akun A, bukan data user asli.
- Baseline capture: simpan response A sebagai `baseline.json`.

## Attack surface
Identifier bisa muncul di lebih dari satu tempat. Enumerasi semuanya:
- path segment : `/api/v1/orders/{id}`
- query param  : `?user_id=`, `?account=`
- JSON body    : `{"orderId": 1024}`
- header       : `X-Account-Id`, `X-Tenant-Id`
- GraphQL      : `node(id:)`, argument pada mutation
- batch/array  : `{"ids":[1024,1025]}` sering lolos authz check

## Mutation vectors
1. Substitusi id langsung: token B ke object milik A.
2. Parameter pollution: `?id=B_id&id=A_id` dan urutan terbalik.
3. Type juggling: `1024` vs `"1024"` vs `[1024]`.
4. Method override: `X-HTTP-Method-Override: PUT` pada endpoint GET.
5. Content-type swap: `application/json` ke `form-urlencoded`.
6. Wrapper object: `{"id":{"$ne":null}}` pada backend NoSQL.
7. ID format: numeric, UUIDv4 bocor dari endpoint list atau export.

## Verifikasi (apa yang bikin ini BUKTI, bukan tebakan)
- Positif = SESS_B berhasil membaca/mengubah object milik SESS_A, dan
  isinya cocok dengan `baseline.json` (read-back terkonfirmasi).
- Bukan positif = beda respons tanpa akses data A yang sebenarnya,
  atau cuma refleksi input. Tandai `no_proof`, buang.
- Simpan: request mentah B, response B, dan diff terhadap baseline.

## Aturan mutlak
- Hanya object milik akun uji sendiri. Dilarang membaca data user asli.
- Dilarang mutasi destruktif di produksi (delete/transfer) tanpa sandbox.
