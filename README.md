# AI-Powered Bug Hunting Starter Kit

Kerangka 3 lapis untuk setup AI bug hunting: **Skill → Agent → Harness**.
Fondasinya skill, bukan tool. Bangun dari bawah ke atas.

```
~/.claude/
├── skills/
│   ├── recon-surface/SKILL.md     # lapis 1: pemetaan permukaan
│   ├── idor-api/SKILL.md          # lapis 1: deteksi IDOR/BOLA
│   └── xss-web/SKILL.md           # lapis 1: deteksi XSS
├── agents/
│   ├── recon.md                   # lapis 2: agent recon (murah/haiku)
│   └── webapp-hunter.md           # lapis 2: agent hunting (sonnet/opus)
└── harness/
    └── vuln-discovery-harness.md  # lapis 3: orkestrasi + verification gate

engagement/
└── engagement-rules.md            # scope + rate limit + aturan (WAJIB diisi)

templates/
└── report-template.md             # template report anti AI slop
```

## Cara pasang

```bash
# dari folder ini
cp -r skills/* ~/.claude/skills/
cp -r agents  ~/.claude/            # kalau kamu pakai subagents
# engagement-rules.md & templates taruh di folder project hunting kamu
```

## Urutan kerja

1. **Isi `engagement/engagement-rules.md` dulu.** Tanpa scope yang benar, jangan jalan.
2. Jalankan agent `recon` buat petakan permukaan.
3. Agent `webapp-hunter` me-route sinyal recon ke skill yang tepat.
4. Harness menjalankan pipeline, menyimpan state, dan memverifikasi temuan lewat gate.
5. Tulis report pakai `templates/report-template.md`.

## PERINGATAN ETIK

Semua ini HANYA untuk target yang kamu punya izin tertulis:
program bug bounty resmi (HackerOne/Bugcrowd/Intigriti/YesWeHack) atau
pentest berkontrak. Aturan scope di agent bukan formalitas — itu pengaman
supaya agent tidak menyentuh aset di luar izin. Kamu yang bertanggung jawab
penuh atas apa yang agent lakukan.
