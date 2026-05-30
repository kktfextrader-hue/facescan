# AXIOM-7 Backup — 2026-05-30

**File:** `axiom7-final.html`  
**Version:** 4.3  
**Lines:** 1382  
**Backed up:** 2026-05-30 01:13

---

## Features in this build

### Core detection
- `detectAllFaces` — multi-face detection พร้อมกัน
- `inputSize: 160` — เร็วขึ้น ~4x จาก 320
- Sort by box area → largest face = primary
- Descriptor + landmarks + pose estimate ทุก face
- Stability tracking แยกต่างหากแต่ละ face (`prevDescs` keyed by index)

### Scan mode
- Capture pose ไหนเจอก่อนได้ก่อน ไม่เรียงลำดับ
- Skip pose ที่ capture แล้ว (`memRef.current[pc]`)
- Progress dot indicator กลางจอ (F · L · R)
- Auto-save เมื่อครบ 3 poses ชื่อ `SUBJ-001`

### Recognition / Freeze
- Match threshold: 90% → IDENTIFIED
- Freeze threshold: 70% → panel ขวาค้าง 30 วินาที
- `isFrozenRef` guard — ป้องกัน reset timer ทุก frame
- Timer bar animation หดลงใน 30s
- `frozenHit` state เก็บข้อมูลที่ freeze

### Database
- Edit ชื่อ + เบอร์โทร inline ใน DBCard
- Delete identity
- Phone แสดงใน recognition overlay และ DBCard
- Phone ใส่ได้ตอน Save Modal (optional)

### Google Sheets sync
- Action: `getAll` (GET), `save` (GET), `delete` (GET), `update` (GET), `editMeta` (GET)
- ใช้ GET ทั้งหมด — POST มี CORS issue กับ GAS
- ไม่เซฟ thumb ใน URL (ใหญ่เกิน) → ใช้ `tinyThumb` 28×28 px quality 0.25 (~800B)
- Descriptor precision 3 decimal (ลด URL size)
- Auto-migrate sheet เก่า: insert `phone` col และ `thumb` col อัตโนมัติ
- Backward compat: รองรับ sheet ที่ไม่มี phone / thumb col

### Thumbnail system
- `makeThumb()` สร้าง 2 ขนาด: `thumb` 96px (local), `tinyThumb` 28px (sheet)
- โหลด tinyThumb กลับจาก sheet ใช้แสดงใน DBCard

### HUD (canvas overlay)
- Primary face: full label (pose, quality, mode, yaw/pitch/roll)
- Secondary faces: lighter HUD, badge FACE 2/3, match label
- Face count badge ล่างกล้อง

### UI structure
- 3 columns: left telemetry | center camera | right panel
- Mode bar: AUTO READ / NEW SCAN / UPDATE DATA
- Log panel ล่าง 5 บรรทัด
- Header: status, FPS, DB count, sync badge, settings button

---

## Constants

| Constant | Value | Purpose |
|---|---|---|
| `Q_THRESH` | 56 | minimum quality score |
| `BLUR_MIN` | 11 | minimum blur score |
| `COOLDOWN` | 3000ms | ระยะเวลารอระหว่าง capture |
| `STAB_MIN` | 53 | minimum stability |
| `FRONT_MAX` | 14° | yaw range สำหรับ FRONTAL |
| `SIDE_MIN` | 17° | yaw เริ่ม LEFT/RIGHT |
| `SIDE_MAX` | 55° | yaw สูงสุด LEFT/RIGHT |
| `CONF_MAXD` | 0.60 | max descriptor distance |
| `MATCH_CONF` | 90% | threshold → IDENTIFIED |
| `FREEZE_THRESH` | 70% | threshold → freeze panel |
| `FREEZE_HOLD` | 30000ms | ระยะเวลา freeze (30 วิ) |

---

## GAS Script actions

| Action | Method | Parameters |
|---|---|---|
| `getAll` | GET | — |
| `save` | GET | `data` (JSON) |
| `delete` | GET | `id` |
| `update` | GET | `id`, `poses` (JSON) |
| `editMeta` | GET | `id`, `name`, `phone` |
| `ping` | GET | — |

### Sheet columns (new format)
`id | name | phone | enrolledAt | thumb | f_desc | f_qual | f_stab | f_blur | l_desc | l_qual | l_stab | l_blur | r_desc | r_qual | r_stab | r_blur`

---

## Known issues / notes
- GAS ต้อง deploy ใหม่ทุกครั้งที่แก้ script
- `thumb` ใน sheet = tinyThumb 28px — คุณภาพต่ำแต่โหลดได้
- หลังเปลี่ยน UI ใหม่ ให้ตรวจสอบ `freezeRef` ที่ยังอยู่ใน refs แต่ไม่ได้ใช้แล้ว (สามารถลบได้)
- `scanWantRef` และ `scanPrompt` ถูกลบออกแล้วใน build นี้

---

## HTML file snapshot info
- ไฟล์หลัก: `axiom7-final.html`
- ไฟล์นี้: `axiom7-backup-notes.md` (notes เท่านั้น ไม่ใช่ full backup)
- Full backup HTML: ดูจาก git history หรือ copy `axiom7-final.html` → `axiom7-final.bak.html`
