# SmartClaim by Dr.K — คู่มือการใช้งาน (ภาษาไทย)

**Version 1.0 | มีนาคม 2569**

---

## สารบัญ

1. [ภาพรวมระบบ](#1-ภาพรวมระบบ)
2. [ความต้องการของระบบ](#2-ความต้องการของระบบ)
3. [การติดตั้ง](#3-การติดตั้ง)
4. [การตั้งค่าเริ่มต้น](#4-การตั้งค่าเริ่มต้น)
5. [การใช้งานพื้นฐาน](#5-การใช้งานพื้นฐาน)
6. [Skills ทั้งหมด 20 ตัว](#6-skills-ทั้งหมด-20-ตัว)
7. [การใช้งาน Skills อย่างละเอียด](#7-การใช้งาน-skills-อย่างละเอียด)
8. [การถอนการติดตั้ง](#8-การถอนการติดตั้ง)
9. [การแก้ปัญหาเบื้องต้น](#9-การแก้ปัญหาเบื้องต้น)
10. [คำถามที่พบบ่อย (FAQ)](#10-คำถามที่พบบ่อย)

---

## 1. ภาพรวมระบบ

**SmartClaim by Dr.K** คือ AI Desktop Application สำหรับตรวจสอบเอกสารเบิกจ่ายเงินจาก สปสช. (สำนักงานหลักประกันสุขภาพแห่งชาติ) ก่อนส่ง FDH/e-Claim

### สิ่งที่ระบบทำได้

| ความสามารถ              | รายละเอียด                                         |
| ----------------------- | -------------------------------------------------- |
| ตรวจเคสก่อนส่งเบิก      | ตรวจ 8 checkpoints, ICD codes, devices, timing     |
| วิเคราะห์ Deny          | หาสาเหตุ + แนะนำวิธีแก้ + ประเมินโอกาส appeal      |
| ร่างหนังสืออุทธรณ์      | สร้าง Word ภาษาราชการไทย พร้อมส่ง                  |
| Auto-code ICD           | แปลง clinical notes → ICD-10 + ICD-9 อัตโนมัติ     |
| Batch ตรวจทั้งเดือน     | ตรวจทุกเคสจาก CSV/Excel พร้อมกัน                   |
| ทำนาย Deny              | คำนวณโอกาสถูก deny ก่อนส่ง                         |
| Dashboard สำหรับหัวหน้า | Revenue, Deny rate, KPI รายเดือน                   |
| รองรับ 8 แผนก           | Cath Lab, OR, ICU, ER, Chemo, Dialysis, OPD, Rehab |

### แผนกที่รองรับ

- Cath Lab (สวนหัวใจ/PCI/Stent)
- OR Surgery (ห้องผ่าตัด)
- ICU/NICU (ผู้ป่วยวิกฤต)
- ER/UCEP (ฉุกเฉิน)
- Chemo (เคมีบำบัด)
- Dialysis (ฟอกไต HD/CAPD)
- OPD/NCD (ผู้ป่วยนอก/โรคเรื้อรัง)
- Rehab (ฟื้นฟู/ประคับประคอง)

---

## 2. ความต้องการของระบบ

### Hardware ขั้นต่ำ

| รายการ       | ขั้นต่ำ             | แนะนำ                 |
| ------------ | ------------------- | --------------------- |
| CPU          | Apple M1 / Intel i5 | Apple M2+ / Intel i7+ |
| RAM          | 8 GB                | 16 GB                 |
| พื้นที่ดิสก์ | 2 GB                | 5 GB                  |
| จอ           | 1280x720            | 1920x1080+            |

### Software ที่ต้องมี

| Software       | Version               | ดาวน์โหลด             |
| -------------- | --------------------- | --------------------- |
| macOS          | 11.0 (Big Sur) ขึ้นไป | -                     |
| Docker Desktop | 4.0+                  | docker.com            |
| Node.js        | 20+                   | nodejs.org            |
| pnpm           | 9+                    | `npm install -g pnpm` |

### API Key (สำหรับ AI Analysis)

ต้องมี API Key จาก AI Provider อย่างน้อย 1 ตัว:

- **Anthropic** (Claude) — แนะนำ
- **OpenAI** (GPT-4)
- **Google AI** (Gemini)

---

## 3. การติดตั้ง

### วิธีที่ 1: ติดตั้งจาก .dmg (แนะนำ)

```
1. ดาวน์โหลดไฟล์ SmartClaim-DrK-x.x.x-mac-arm64.dmg
2. เปิดไฟล์ .dmg
3. ลาก SmartClaim by Dr.K ไปใส่ Applications
4. เปิด app จาก Applications
5. (ครั้งแรก) macOS จะถามว่า "ต้องการเปิดหรือไม่" → กด Open
```

### วิธีที่ 2: Build จาก Source Code

```bash
# 1. Clone repository
git clone https://github.com/ballbadboy/accomplish.git
cd accomplish

# 2. ติดตั้ง dependencies
pnpm install

# 3. Build
pnpm build

# 4. สร้าง .dmg
pnpm package:mac

# 5. ไฟล์ .dmg จะอยู่ใน dist/
ls dist/*.dmg
```

### ติดตั้ง Backend (สำหรับ Full Features)

Backend จำเป็นสำหรับ: ตรวจเคสผ่าน API, เก็บข้อมูลใน Database, Dashboard

```bash
# 1. Clone backend
git clone https://github.com/ballbadboy/smartclaim-drk.git
cd smartclaim-drk/apps/backend

# 2. สร้างไฟล์ .env
cp .env.example .env
# แก้ไข ANTHROPIC_API_KEY ในไฟล์ .env

# 3. เปิด Docker
docker compose up -d

# 4. สร้าง tables ในฐานข้อมูล
docker compose exec -w /app api python -c "
from core.database import Base, get_engine
import asyncio
async def create():
    engine = get_engine()
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
asyncio.run(create())
"

# 5. สร้าง user
docker compose exec -w /app api python -m scripts.create_admin <username> <password>

# 6. ทดสอบ
curl http://localhost:8001/docs
# → เห็น Swagger UI = สำเร็จ
```

### ติดตั้ง Dashboard (Optional)

```bash
cd smartclaim-drk/apps/dashboard
pnpm install
pnpm dev
# เปิด http://localhost:3000
```

---

## 4. การตั้งค่าเริ่มต้น

### 4.1 เปิด App ครั้งแรก

1. เปิด SmartClaim by Dr.K จาก Applications
2. จะเห็นหน้า Setup — ให้เลือก AI Provider

### 4.2 ตั้งค่า AI Provider

1. กด **Settings** (ไอคอนเฟือง ล่างซ้าย)
2. ไปที่ **Providers**
3. เลือก Provider ที่ต้องการ:

| Provider                | วิธีตั้งค่า                                        |
| ----------------------- | -------------------------------------------------- |
| **Anthropic** (แนะนำ)   | ใส่ API Key จาก console.anthropic.com              |
| **OpenAI**              | ใส่ API Key จาก platform.openai.com                |
| **Google AI**           | ใส่ API Key จาก aistudio.google.com                |
| **Ollama** (ฟรี, local) | ติดตั้ง Ollama แล้วดึง model: `ollama pull llama3` |

4. กด **Test Connection** → ถ้าเห็น "Connected" = สำเร็จ

### 4.3 ตรวจสอบ Skills

1. ไปที่ **Settings → Skills**
2. จะเห็น SmartClaim skills 20 ตัว (เขียนว่า "Built By You")
3. ตรวจว่าทุก skill เปิด (toggle สีเขียว)
4. กด **Done**

### 4.4 ตั้งค่า Backend Connection (Optional)

ถ้าติดตั้ง Backend แล้ว ต้องบอก AI ว่า Backend อยู่ที่ไหน:

- พิมพ์ใน chat: `Backend อยู่ที่ http://localhost:8001`
- AI จะจำไว้และใช้ตลอด session

---

## 5. การใช้งานพื้นฐาน

### 5.1 หน้าจอหลัก

```
┌─────────────────────────────────────────────┐
│  SmartClaim by Dr.K                          │
├──────────┬──────────────────────────────────┤
│          │                                   │
│ New Task │  What will you accomplish today?  │
│ Search   │                                   │
│ Scheduled│  [พิมพ์คำสั่งตรงนี้]                │
│          │                                   │
│ Recent:  │  Example Prompts:                 │
│ • task1  │  • Stock Tracker                  │
│ • task2  │  • Daily Hackernews               │
│          │  • Tidy up Desktop                │
│          │                                   │
│ Settings │                                   │
└──────────┴──────────────────────────────────┘
```

### 5.2 วิธีใช้งาน — พิมพ์ภาษาไทยได้เลย

**ตัวอย่างคำสั่ง:**

| คำสั่ง                                  | สิ่งที่ AI ทำ                     |
| --------------------------------------- | --------------------------------- |
| `ตรวจเคส Cath Lab I21.0 PCI 36.06`      | ตรวจ 8 checkpoints + แนะนำ CC/MCC |
| `เคส AN 69-03556 โดน deny HC09`         | วิเคราะห์สาเหตุ + แนะนำวิธีแก้    |
| `สร้าง appeal สำหรับ deny HC09`         | ร่างหนังสืออุทธรณ์ภาษาราชการ      |
| `code ให้หน่อย: ผู้ป่วยชาย 65 ปี STEMI` | แปลง clinical notes → ICD codes   |
| `ตรวจเคสทั้งเดือน มี.ค.`                | Batch ตรวจทุกเคสจาก HIS/CSV       |
| `ทำนาย deny เคส I21.0 + 36.06`          | ประเมินโอกาสถูก deny 0-100%       |
| `สร้าง board report เดือน มี.ค.`        | สร้างรายงานสำหรับ ผอ.             |

### 5.3 การแนบไฟล์

**รองรับ:** .txt, .md, .csv, .json, .png, .jpg
**ไม่รองรับ (ยัง):** .xlsx, .xls, .docx (ใช้วิธีบอก path แทน)

**วิธีใช้กับ Excel:**

```
วิเคราะห์ไฟล์ /Users/ballbadboy/Downloads/eclaim_report.xlsx
```

**วิธีใช้กับ CSV:**

- ลากไฟล์ .csv เข้า chat ได้โดยตรง

---

## 6. Skills ทั้งหมด 20 ตัว

### กลุ่ม A: ตรวจเคสรายแผนก (7 ตัว)

| #   | Skill                  | คำสั่ง                  | ใช้เมื่อ              |
| --- | ---------------------- | ----------------------- | --------------------- |
| 1   | **Cath Lab Checker**   | `cathlab-claim-checker` | เคสสวนหัวใจ PCI Stent |
| 2   | **Cath Lab Portable**  | `cathlab-claim-ai`      | เวอร์ชันรวมครบจบในตัว |
| 3   | **OR Surgery Checker** | `or-surgery-checker`    | เคสผ่าตัดทุกชนิด      |
| 4   | **ICU/NICU Checker**   | `icu-checker`           | เคสผู้ป่วยวิกฤต       |
| 5   | **ER/UCEP Checker**    | `er-ucep-checker`       | เคสฉุกเฉิน            |
| 6   | **Chemo Checker**      | `chemo-checker`         | เคสเคมีบำบัด          |
| 7   | **Dialysis Checker**   | `dialysis-checker`      | เคสฟอกไต              |

### กลุ่ม B: ตรวจเคสทั่วไป (3 ตัว)

| #   | Skill               | คำสั่ง                  | ใช้เมื่อ                     |
| --- | ------------------- | ----------------------- | ---------------------------- |
| 8   | **Claim Validator** | `claim-validator`       | ตรวจเคสทุกแผนก 8 checkpoints |
| 9   | **OPD/NCD Checker** | `opd-ncd-checker`       | เคสผู้ป่วยนอก/โรคเรื้อรัง    |
| 10  | **Batch Optimizer** | `batch-claim-optimizer` | ตรวจทั้งเดือนจาก CSV         |

### กลุ่ม C: วิเคราะห์ Deny + อุทธรณ์ (3 ตัว)

| #   | Skill              | คำสั่ง           | ใช้เมื่อ             |
| --- | ------------------ | ---------------- | -------------------- |
| 11  | **Deny Analyzer**  | `deny-analyzer`  | วิเคราะห์สาเหตุ deny |
| 12  | **Deny Predictor** | `deny-predictor` | ทำนายโอกาสถูก deny   |
| 13  | **Appeal Drafter** | `appeal-drafter` | ร่างหนังสืออุทธรณ์   |

### กลุ่ม D: Coding + AI (3 ตัว)

| #   | Skill                 | คำสั่ง                    | ใช้เมื่อ                         |
| --- | --------------------- | ------------------------- | -------------------------------- |
| 14  | **Smart Coder**       | `smart-coder`             | แปลง notes → ICD codes อัตโนมัติ |
| 15  | **ICD Coding**        | `icd-coding`              | ช่วย coding ICD-10/ICD-9         |
| 16  | **Knowledge Updater** | `claim-knowledge-updater` | อัพเดตฐานความรู้                 |

### กลุ่ม E: Management + Reports (4 ตัว)

| #   | Skill                   | คำสั่ง                   | ใช้เมื่อ                    |
| --- | ----------------------- | ------------------------ | --------------------------- |
| 17  | **Revenue Tracker**     | `revenue-tracker`        | ติดตามรายได้ claim          |
| 18  | **Board Report**        | `board-report-generator` | สร้างรายงาน ผอ./กรรมการ     |
| 19  | **Financial Simulator** | `financial-simulator`    | จำลอง what-if การเงิน       |
| 20  | **Drug Cost Optimizer** | `drug-cost-optimizer`    | ลดต้นทุนยา original→generic |

---

## 7. การใช้งาน Skills อย่างละเอียด

### 7.1 Cath Lab Checker (สวนหัวใจ)

**เมื่อไหร่ใช้:** มีเคส PCI/Stent/Cath ที่จะส่งเบิก สปสช.

**วิธีใช้:**

```
ตรวจเคส Cath Lab
AN: 69-03556
HN: 12345
PDx: I21.0 (STEMI anterior wall)
SDx: E11.9, I48.0
Procedures: 36.06, 88.56, 37.22
Stent: DES Xience 3.0x28mm lot ABC123 จำนวน 1
สิทธิ: UC
Admit: 1/3/69
Discharge: 5/3/69
```

**ผลลัพธ์ที่ได้:**

```
Score: 85/100 ✅ พร้อมส่งเบิก

✅ Checkpoint 1: Dx-Proc Match — STEMI + PCI ตรงกัน
✅ Checkpoint 2: Clinical Docs — Troponin, EKG, Cath report OK
✅ Checkpoint 3: Device — Stent มี lot number ครบ
✅ Checkpoint 4: 16-File — format ถูกต้อง
✅ Checkpoint 5: Timing — ส่งภายใน 30 วัน
⚡ Checkpoint 6: CC/MCC — มี CC (E11.9 DM) แต่ตรวจว่ามี MCC ไหม
✅ Checkpoint 7: Department — auto-detect Cath Lab
✅ Checkpoint 8: Score — 85/100

DRG: MDC05 PCI w/ CC → RW 5.2 → ≈ ฿62,400

แนะนำ:
- เพิ่ม N17.9 (AKI) ถ้าผู้ป่วยมี Cr สูง → RW เพิ่มเป็น 5.8 (+฿7,200)
```

### 7.2 OR Surgery Checker (ห้องผ่าตัด)

**เมื่อไหร่ใช้:** มีเคสผ่าตัด (Ortho, Neuro, GI, GYN, etc.)

**วิธีใช้:**

```
ตรวจเคสผ่าตัด
AN: 69-04100
PDx: K80.10 (Calculus of gallbladder with cholecystitis)
Procedures: 51.22 (Cholecystectomy)
Implant: ไม่มี
สิทธิ: UC
Admit: 10/3/69
Discharge: 13/3/69
```

### 7.3 Deny Analyzer (วิเคราะห์ Deny)

**เมื่อไหร่ใช้:** เคสถูก สปสช. deny แล้ว ต้องการรู้สาเหตุและวิธีแก้

**วิธีใช้:**

```
เคส AN 69-03556 โดน deny code HC09
PDx: I21.0
Procedures: 36.06
```

**ผลลัพธ์:**

```
Deny Code: HC09
ความหมาย: Serial number อุปกรณ์ไม่ตรง GPO VMI record
หมวด: Device Documentation
โอกาส Recovery: 85%
แนะนำ: APPEAL

วิธีแก้:
1. ตรวจสอบ lot/serial number ใน ADP file
2. เทียบกับ GPO VMI/SMI procurement record
3. แนบใบส่งของจากบริษัท stent
4. แนบ GPO VMI record ที่ตรงกัน

เอกสารแนบสำหรับ appeal:
- สำเนา ADP file (แก้ไขแล้ว)
- GPO VMI record
- ใบส่งของจากบริษัท
- Procedure note ที่ระบุ stent ที่ใช้
```

### 7.4 Appeal Drafter (ร่างอุทธรณ์)

**เมื่อไหร่ใช้:** ต้องการหนังสืออุทธรณ์พร้อมส่ง สปสช.

**วิธีใช้:**

```
สร้าง appeal
AN: 69-03556
Deny code: HC09
ชื่อ รพ.: โรงพยาบาลพญาไทศรีราชา
ผู้ลงนาม: นพ.สมชาย ใจดี
ตำแหน่ง: ผู้อำนวยการโรงพยาบาล
```

**ผลลัพธ์:** ได้ไฟล์ Word (.docx) หนังสืออุทธรณ์ภาษาราชการไทย พร้อมหัวจดหมาย

### 7.5 Smart Coder (Auto-code ICD)

**เมื่อไหร่ใช้:** มี clinical notes ต้องการแปลงเป็น ICD codes

**วิธีใช้:**

```
code ให้หน่อย:
ผู้ป่วยชาย 65 ปี มาด้วยเจ็บหน้าอกรุนแรง 2 ชม. ก่อนมา
EKG: ST elevation V1-V4
Troponin I: 15.2 ng/mL (สูงมาก)
ทำ PCI ใส่ stent LAD 1 ตัว (DES)
มีโรคประจำตัว DM, HT, CKD stage 3
Post-PCI develop AKI Cr 2.8
```

**ผลลัพธ์:**

```
Principal Dx: I21.0 — STEMI anterior wall
Secondary Dx:
  - N17.9 — Acute kidney injury (MCC! +RW 20-40%)
  - E11.9 — DM type 2 (CC)
  - I10 — Essential hypertension (ไม่มีผลต่อ RW)
  - N18.3 — CKD stage 3 (CC)

Procedures:
  - 36.06 — PCI with drug-eluting stent
  - 88.56 — Coronary angiography
  - 37.22 — Left heart catheterization

Expected DRG: MDC05 PCI w/ MCC
Estimated RW: 5.8

แนะนำ: อย่าลืม code N17.9 (AKI) เป็น MCC → เพิ่ม RW อีก 20-40%!
```

### 7.6 Deny Predictor (ทำนาย Deny)

**เมื่อไหร่ใช้:** ต้องการรู้ก่อนส่งว่าเคสจะถูก deny หรือไม่

**วิธีใช้:**

```
ทำนาย deny
PDx: I21.0
Procedures: 36.06, 88.56
Stent: DES 1 ตัว (มี lot)
สิทธิ: UC
LOS: 4 วัน
CC/MCC: E11.9
```

**ผลลัพธ์:**

```
Deny Probability: 12% (ต่ำ — ส่งได้)

Risk Factors:
  ✅ Dx-Proc match: OK (0%)
  ✅ Device docs: มี lot (0%)
  ✅ Timing: ภายใน 30 วัน (0%)
  ✅ CC/MCC: มี CC (0%)
  ⚠️ Authen Code: ไม่มี (+10%)
  ✅ LOS: ปกติ (0%)
  ⚠️ Claim amount: สูง (+2%)

แนะนำ: Verify ผู้ป่วยเพื่อรับ Authen Code ก่อนส่ง
```

### 7.7 Batch Claim Optimizer (ตรวจทั้งเดือน)

**เมื่อไหร่ใช้:** ต้องการตรวจเคสทั้งเดือนก่อนส่ง FDH

**วิธีใช้:**

```
ตรวจเคสทั้งเดือนจากไฟล์
/Users/ballbadboy/Downloads/eclaim_march.xlsx
```

หรือ:

```
batch check เดือน มี.ค. 69 แผนก Cath Lab
```

**ผลลัพธ์:**

```
Batch Results — มี.ค. 2569

ทั้งหมด: 10 เคส
  🔴 Critical: 2 เคส (ห้ามส่ง)
  🟡 Warning: 3 เคส (ส่งได้แต่ควรตรวจ)
  🟢 Ready: 5 เคส (พร้อมส่ง)
  ⚡ Optimize: 4 เคส (เพิ่ม CC/MCC ได้)

Revenue:
  ประมาณทั้งหมด: ฿720,000
  ถ้า optimize ครบ: ฿890,000 (+฿170,000)

Priority:
  1. AN 69-03601 — ❌ ไม่มี Authen Code
  2. AN 69-03612 — ❌ Stent ไม่มี lot
  3. AN 69-03556 — ⚡ เพิ่ม N17.9 → +฿7,200
  ...
```

### 7.8 Board Report Generator (รายงาน ผอ.)

**เมื่อไหร่ใช้:** ต้องการรายงานประจำเดือนสำหรับ ผอ./กรรมการ

**วิธีใช้:**

```
สร้าง board report เดือน มี.ค. 2569
แผนก: Cath Lab
รพ.: พญาไทศรีราชา
```

### 7.9 Revenue Tracker (ติดตามรายได้)

**เมื่อไหร่ใช้:** ต้องการรู้ว่า claim ที่ส่งไป ได้เงินแล้วหรือยัง

**วิธีใช้:**

```
ติดตามรายได้เดือน มี.ค.
```

### 7.10 Financial Simulator (จำลองการเงิน)

**เมื่อไหร่ใช้:** ต้องการรู้ว่า "ถ้าเปลี่ยน X จะกระทบ Y อย่างไร"

**วิธีใช้:**

```
ถ้าเพิ่มเคส PCI จาก 10 เป็น 15 ต่อเดือน รายได้จะเพิ่มเท่าไหร่
```

```
ถ้าลด deny rate จาก 12% เหลือ 5% จะได้เงินคืนเท่าไหร่
```

### 7.11 Drug Cost Optimizer (ลดต้นทุนยา)

**เมื่อไหร่ใช้:** ต้องการลดต้นทุนยาโดย switch จาก original → generic

**วิธีใช้:**

```
วิเคราะห์ต้นทุนยา Cath Lab
ยาที่ใช้: Clopidogrel, Atorvastatin, Enoxaparin
```

---

## 8. การถอนการติดตั้ง

### ถอน Desktop App

```bash
# 1. ปิด SmartClaim by Dr.K

# 2. ลบ app
rm -rf "/Applications/SmartClaim by Dr.K.app"

# 3. ลบข้อมูล app
rm -rf "$HOME/Library/Application Support/Accomplish"

# 4. ลบ cache
rm -rf "$HOME/Library/Caches/SmartClaim by Dr.K"
rm -rf "$HOME/Library/Caches/com.drk.smartclaim"
```

### ถอน Backend (Docker)

```bash
# 1. หยุด containers
cd smartclaim-drk/apps/backend
docker compose down

# 2. ลบ volumes (ข้อมูลทั้งหมดจะหาย!)
docker compose down -v

# 3. ลบ images
docker rmi smartclaim-drk-api
```

### ถอนทั้งหมด (Clean)

```bash
# ลบ app
rm -rf "/Applications/SmartClaim by Dr.K.app"

# ลบ app data
rm -rf "$HOME/Library/Application Support/Accomplish"

# ลบ source code
rm -rf ~/Desktop/projectX/accomplish
rm -rf ~/Desktop/projectX/smartclaim-drk

# ลบ Docker
cd smartclaim-drk/apps/backend && docker compose down -v
```

---

## 9. การแก้ปัญหาเบื้องต้น

### App เปิดไม่ได้ / Crash

```bash
# ลบ cache แล้วเปิดใหม่
rm -rf "$HOME/Library/Application Support/Accomplish/Cache"
rm -rf "$HOME/Library/Application Support/Accomplish/Code Cache"
open "/Applications/SmartClaim by Dr.K.app"
```

### Backend เชื่อมต่อไม่ได้

```bash
# ตรวจว่า Docker ทำงาน
docker compose ps

# ดู logs
docker compose logs api --tail 50

# restart
docker compose restart api
```

### AI ไม่ตอบ / ช้ามาก

1. ตรวจว่า API Key ถูกต้อง: **Settings → Providers → Test Connection**
2. ตรวจว่ามียอดเงินคงเหลือใน API account
3. ลองเปลี่ยน model เป็นตัวเล็กกว่า (claude-haiku, gpt-4o-mini)

### Skills ไม่แสดง

```bash
# ตรวจว่า skills อยู่ใน DB
sqlite3 "$HOME/Library/Application Support/Accomplish/accomplish.db" \
  "SELECT name, is_enabled FROM skills WHERE source='custom';"
```

### ไฟล์ Excel เปิดไม่ได้

- ไฟล์ .xls (เก่า) → แปลงเป็น .xlsx ก่อน (เปิดใน Excel → Save As .xlsx)
- ไฟล์ .xlsx → บอก path ตรงๆ ใน chat: `วิเคราะห์ /path/to/file.xlsx`

---

## 10. คำถามที่พบบ่อย

### Q: ใช้ได้กี่เครื่อง?

A: ขึ้นอยู่กับ license — ระบบรองรับ IP lock สำหรับ multi-hospital deployment

### Q: ข้อมูลผู้ป่วยปลอดภัยมั้ย?

A: ข้อมูล PHI ไม่ถูกส่งไป cloud — Rule Engine ตรวจบน local, AI ได้รับแค่ ICD codes (ไม่มีชื่อ/HN)

### Q: ต้องต่อ internet มั้ย?

A: ต้อง สำหรับ AI analysis (API call) แต่ Rule Engine ทำงาน offline ได้

### Q: รองรับ HIS อะไรบ้าง?

A: SSB (Smart Hospital), HOSxP (MySQL), CSV import — เพิ่มได้ผ่าน connector plugin

### Q: อัพเดต knowledge ยังไง?

A: ใช้ skill `claim-knowledge-updater` — ป้อนไฟล์ PDF/markdown/YouTube แล้วระบบอัพเดตให้อัตโนมัติ

### Q: กฎ สปสช. เปลี่ยน ต้องทำอะไร?

A: ใช้ `claim-knowledge-updater` อัพเดต knowledge files → ทุก skill จะใช้กฎใหม่ทันที

### Q: ราคาเท่าไหร่?

A: ติดต่อ Dr.K — ขาย license per module per hospital

---

## ติดต่อ

**SmartClaim by Dr.K**

- GitHub: github.com/ballbadboy/accomplish
- Developer: Dr.K

---

_Copyright 2026 Dr.K. All rights reserved._
