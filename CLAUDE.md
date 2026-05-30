# CLAUDE.md — Facescan

Single Source of Truth — อ่านไฟล์นี้ก่อนทุกครั้ง  
ข้อมูลลับ (PAT token) อยู่ใน `CLAUDE.local.md` (ไม่ commit)

---

## 1. Project Identity

| รายการ | ข้อมูล |
|---|---|
| ชื่อโปรเจกต์ | AXIOM-7 Biometric Terminal |
| GitHub | kktfextrader-hue/facescan (branch: main) |
| Live URL | https://kktfextrader-hue.github.io/facescan/ |
| ไฟล์หลัก | `axiom7-final.html` (push เป็น `index.html` + `main.html`) |

---

## 2. Tech Stack

- Frontend: Vanilla HTML / React (CDN) / face-api.js
- Database: Google Sheets (via GAS Web App)
- Hosting: GitHub Pages

---

## 3. Deploy Workflow

```bat
REM รัน push-now.bat บน Windows
C:\Users\acer\Desktop\Claude\face detect project\push-now.bat
```

หรือ manual (bash sandbox):

```bash
SRC="/sessions/.../mnt/face detect project"
REPO=/tmp/FS-push

cp "$SRC/axiom7-final.html" "$REPO/index.html"
cp "$SRC/axiom7-final.html" "$REPO/main.html"
cp "$SRC/axiom7-backup-notes.md" "$REPO/axiom7-backup-notes.md"

cd $REPO && git add -A
git commit -m "feat: vX.X — [description]"
git push https://[PAT]@github.com/kktfextrader-hue/facescan.git main
```

**กฎ:** PAT token เก็บเฉพาะใน `CLAUDE.local.md` — ห้ามใส่ในไฟล์ที่ commit

---

## 4. Backup Convention

ทุกครั้งที่แก้ไขเสร็จ ก่อน push:
1. สร้าง `axiom7-backup-notes.md` ใหม่ (อัปเดต version + สิ่งที่เปลี่ยน)
2. สร้าง `axiom7-final.bak.html` (copy ของ HTML ปัจจุบัน)
3. Push ทั้งหมดขึ้น GitHub

Files ที่ push ทุกครั้ง:
- `index.html` ← copy จาก `axiom7-final.html`
- `main.html` ← copy จาก `axiom7-final.html`
- `axiom7-final.html`
- `axiom7-final.bak.html`
- `axiom7-backup-notes.md`
- `CLAUDE.md`

---

## 5. Version & Constants

| Constant | Value | Purpose |
|---|---|---|
| VER | 4.3 | app version |
| Q_THRESH | 56 | min quality score |
| BLUR_MIN | 11 | min blur score |
| COOLDOWN | 3000ms | delay between captures |
| STAB_MIN | 53 | min stability |
| MATCH_CONF | 90% | threshold → IDENTIFIED |
| FREEZE_THRESH | 70% | threshold → freeze panel |
| FREEZE_HOLD | 30000ms | freeze duration (30s) |

---

## 6. Google Sheets / GAS

Actions ทั้งหมดใช้ GET:
`getAll` / `save` / `delete` / `update` / `editMeta` / `ping`

Sheet columns: `id | name | phone | enrolledAt | thumb | f_desc | f_qual | f_stab | f_blur | l_desc | l_qual | l_stab | l_blur | r_desc | r_qual | r_stab | r_blur`

---

## 7. สิ่งที่ห้ามทำ

- ❌ ใส่ PAT token ในไฟล์ที่ commit
- ❌ เซฟ base64 thumb ใน URL (ใหญ่เกิน) — ใช้ tinyThumb 28px แทน
- ❌ ใช้ POST กับ GAS (CORS issue) — ใช้ GET ทั้งหมด

---

## 8. Changelog

| วันที่ | Version | การเปลี่ยนแปลง |
|---|---|---|
| 2026-05-30 | v4.3 | Multi-face detection, freeze panel 30s, edit name/phone, scan any pose first, inputSize 160 |
