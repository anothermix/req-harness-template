---
name: setup-harness
description: Bootstrap requirement harness สำหรับโปรเจกต์ใหม่ — สร้าง Airtable base (Requirements/Meetings/Open Questions/Stakeholders), เขียน harness.config.json, และปรับ interview guide ให้เข้ากับโปรเจกต์ ใช้เมื่อผู้ใช้เพิ่ง clone template นี้มา หรือพูดว่า "setup harness", "เริ่มโปรเจกต์เก็บ requirement"
---

# Setup Harness

Bootstrap harness เก็บ requirement สำหรับโปรเจกต์ใหม่ ต้องมี Airtable connector (MCP) เชื่อมอยู่ — ถ้าเรียก tool แล้วไม่เจอ ให้บอกผู้ใช้เชื่อม Airtable ก่อน

## ขั้นตอน

### 1. เก็บข้อมูลโปรเจกต์

ถามผู้ใช้ (ข้อไหนรู้จากบทสนทนาแล้วไม่ต้องถามซ้ำ):
- ชื่อโปรเจกต์ + ระบบอะไร ทำให้ใคร แก้ปัญหาอะไร
- มีเอกสารตัวอย่าง/ของเดิมให้วิเคราะห์ตั้งต้นไหม (ฟอร์มเดิม, ระบบเก่า, spec เก่า)
- ใครคือ stakeholder หลักที่ต้องสัมภาษณ์ (บทบาท ไม่ต้องมีชื่อครบ)
- ภาษาของเอกสาร (default: ไทย)

จากคำตอบ ให้เสนอรายการ **modules** ของระบบ (5–9 อัน + "Other") ให้ผู้ใช้ปรับก่อนสร้าง base เพราะแก้ทีหลังยุ่งกว่า

### 2. สร้าง Airtable base

ใช้ `list_workspaces` เลือก workspace (ถามถ้ามีหลายอัน) แล้ว `create_base` ชื่อ `<ชื่อโปรเจกต์> – Requirements` โครงตาราง 4 ตาราง:

- **Requirements**: REQ-ID (singleLineText, primary), Title (singleLineText), Description (multilineText), Type (singleSelect: Functional / Non-functional / Data / UI/UX), Module (singleSelect: ตาม modules ที่ตกลง), Priority (singleSelect: Must / Should / Could / Won't (this phase) — สีแดง/ส้ม/เหลือง/เทา), Status (singleSelect: Draft / Confirmed / Approved / In Spec / Rejected), Phase (singleSelect: MVP / Phase 2 / Phase 3 / TBD), Requested By (singleLineText), Acceptance Criteria (multilineText), Notes (multilineText)
- **Meetings**: Meeting (singleLineText, primary), Date (date, iso), Meeting Type (singleSelect: Interview / Workshop / Playback/Review / Sign-off / Other), Attendees, Summary, Decisions, Action Items (multilineText ทั้งหมด), Fireflies Link (url)
- **Open Questions**: Question (multilineText, primary), Ask Who (singleLineText), Status (singleSelect: Open / Answered / Dropped), Answer (multilineText)
- **Stakeholders**: Name (singleLineText, primary), Role (singleSelect: ตามบทบาทจากข้อ 1), Contact (singleLineText), Interviewed (checkbox), Notes (multilineText)

จากนั้น `create_field` เพิ่ม link fields:
- Requirements → "Source Meetings" (multipleRecordLinks → Meetings)
- Open Questions → "Raised In" (→ Meetings) และ "Related Requirements" (→ Requirements)

### 3. เขียน config

เขียน `harness.config.json` ที่ root ของ repo ตามโครงใน `harness.config.example.json` — เอา base/table/field IDs จากผลลัพธ์ขั้นที่ 2 มาใส่ให้ครบทุก field ห้ามปล่อยเป็น placeholder skill `/meeting-to-req` และ `/build-spec` อ่านไฟล์นี้เป็นแหล่งเดียว

### 4. Seed ตั้งต้น (ถ้ามีเอกสารตัวอย่าง)

ถ้าผู้ใช้มีเอกสาร/ระบบเดิมให้วิเคราะห์: อ่านแล้วสกัดเป็น requirement ตั้งต้นลงตาราง Requirements (REQ-001 เป็นต้นไป, Status=Draft, Requested By="วิเคราะห์จากเอกสารตัวอย่าง") พร้อม Open Questions ที่การวิเคราะห์ตอบไม่ได้ — ย้ำกับผู้ใช้ว่าของ seed เป็น Draft ต้องเอาเข้าประชุมยืนยันก่อน

### 5. Interview guide

เขียน `docs/interview-guide.md` จากโครงใน `docs/interview-guide.template.md` แทนที่ placeholder ด้วยคำถามจริงตามบริบทโปรเจกต์ แยก section ตาม stakeholder ที่ระบุในข้อ 1

### 6. ปิดงาน

- เติม section "โปรเจกต์นี้" ใน README.md (ชื่อ, ลิงก์ Airtable base, วันที่ setup)
- แนะนำผู้ใช้: commit การเปลี่ยนแปลง, เชื่อม Fireflies ถ้ายังไม่เชื่อม, นัดประชุม stakeholder คิวแรก
- สรุปเป็นภาษาไทยว่าสร้างอะไรไปบ้าง พร้อมลิงก์ base

## กติกา

- config เป็น source of truth ของ IDs — อย่า hardcode IDs ลงใน skill อื่นหรือเอกสาร
- ถ้า `harness.config.json` มีอยู่แล้วและมี baseId จริง แปลว่า setup ไปแล้ว — ถามผู้ใช้ก่อนว่าจะสร้าง base ใหม่ทับ config เดิมจริงไหม
