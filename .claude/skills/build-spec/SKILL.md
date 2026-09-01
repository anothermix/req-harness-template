---
name: build-spec
description: ดึง requirement จาก Airtable tracker (IDs จาก harness.config.json) มา generate เอกสาร functional spec เป็นไฟล์ markdown ใน docs/spec/ ใช้เมื่อผู้ใช้ขอ "ทำ spec", "รวม requirement เป็นเอกสาร", หรือเตรียมเอกสารสำหรับ playback/sign-off
---

# Build Spec

Generate functional spec จาก Requirement Tracker — เอกสารเป็นผลลัพธ์ของ tracker เสมอ ห้ามแก้เนื้อหา requirement ในเอกสารโดยไม่กลับไปแก้ที่ tracker

## ก่อนเริ่ม

อ่าน `harness.config.json` ที่ root ของ repo — base/table/field IDs และชื่อโปรเจกต์อยู่ที่นั่น
ถ้าไฟล์ไม่มี หรือ IDs ยังเป็น placeholder → หยุด แล้วบอกผู้ใช้รัน `/setup-harness` ก่อน

## ขั้นตอน

1. **ดึงข้อมูล**: `list_records_for_table` ตาราง Requirements ทั้งหมด + Open Questions ที่ยัง Open
2. **ถาม scope ถ้าผู้ใช้ไม่ระบุ**: เอาเฉพาะ Confirmed/Approved (สำหรับ sign-off) หรือรวม Draft ด้วย (สำหรับ playback ระหว่างทาง) — default คือรวม Draft แต่ติดป้ายสถานะให้เห็นชัด
3. **เขียนเอกสาร** ลง `docs/spec/functional-spec.md` โครงสร้าง:
   - หัวเอกสาร: ชื่อโปรเจกต์ (จาก config), วันที่ generate, สรุปจำนวน requirement ตามสถานะ
   - ภาพรวมระบบและ scope แยกตาม Phase
   - เนื้อหาแยกตาม Module → ในแต่ละ module เรียงตาม REQ-ID: หัวข้อ `REQ-XXX ชื่อ [สถานะ] [Priority] [Phase]` ตามด้วย Description และ Acceptance Criteria
   - ท้ายเอกสาร: ตาราง Open Questions ที่ยังค้าง และรายการ requirement ที่ Rejected (เพื่อกันหยิบกลับมาโดยไม่รู้)
4. **เอกสารประกอบ playback**: ถ้าผู้ใช้ขอเวอร์ชันสำหรับประชุม ให้ทำ `docs/spec/playback-summary.md` สั้นๆ เน้นสิ่งที่เปลี่ยนจากรอบก่อนและคำถามที่ต้องการคำตอบ
5. ส่งไฟล์ให้ผู้ใช้ด้วย SendUserFile และสรุปสั้นๆ ว่ามี requirement กี่ข้อ ค้างคำถามกี่ข้อ

## กติกา

- ทุกครั้งที่ generate ให้เขียนทับไฟล์เดิม (git เป็นตัวเก็บประวัติ) และระบุวันที่ generate ในเอกสาร
- ถ้าเจอ requirement ที่ข้อมูลไม่ครบ (ไม่มี Module/Priority) ให้ generate ต่อได้ แต่ list ไว้ท้ายรายงานให้ผู้ใช้ไปเติมใน Airtable
- ภาษาเอกสารตาม `language` ใน config ศัพท์เทคนิคทับศัพท์ได้
