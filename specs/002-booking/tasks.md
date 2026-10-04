# Tasks: การจองงานบริการซ่อมบำรุง (UC-07)

Feature: SPEC-BOOKING | Spec ID: SPEC-BOOKING | อ้างอิง: plan.md | วันที่ 2569-10-04
สรุป: 11 task | 2 task รอ Q-xx (T-05 สำหรับ Q-19, T-06 สำหรับ Q-14)

### T-01 ตั้งโครงโปรเจกต์พื้นฐาน
- รองรับ: REQ-DAT-002, REQ-PRV-002, REQ-CON-001
- ตรวจด้วย: ไม่มี AC ตรง ๆ เป็นงานพื้นฐานให้ task ต่อไปเริ่มได้
- ไฟล์ที่แตะ: backend/requirements.txt, backend/pytest.ini, backend/app/config.py, backend/app/db/session.py, backend/app/db/models.py, backend/app/db/migrations/m001_init.py, backend/app/main.py, backend/tests/conftest.py, frontend/package.json, frontend/vite.config.js, frontend/src/index.css
- ต้องทำหลัง: ไม่มี
- เสร็จเมื่อ: pytest หรือ npm test รันได้ และ upgrade(engine) สร้าง schema เริ่มต้นได้
- สถานะ: เสร็จ รอทีมตรวจ

### T-02 สร้างโมเดลข้อมูลและประวัติสถานะ
- รองรับ: REQ-DAT-002, REQ-PRV-002, AS-03, AS-08
- ตรวจด้วย: ไม่มี AC ตรง ๆ เป็นงานฐานข้อมูลสำหรับทุก workflow
- ไฟล์ที่แตะ: backend/app/db/models.py, backend/app/db/migrations/m001_init.py, backend/app/clock.py
- ต้องทำหลัง: T-01
- เสร็จเมื่อ: ตาราง Job, TimeSlot, Payment, Refund, JobStatusHistory และ ServiceAddress สามารถสร้างได้พร้อมแยก PII อย่างชัดเจน
- สถานะ: เสร็จ รอทีมตรวจ

### T-03 ค้นช่างว่างและล็อกช่วงเวลา
- รองรับ: REQ-FN-008, REQ-FN-041, BR-02, BR-03, AS-06, AS-07
- ตรวจด้วย: AC-07-01, AC-07-02, AC-07-04
- ไฟล์ที่แตะ: backend/app/technicians/service.py, backend/app/technicians/router.py, backend/app/booking/service.py, backend/app/booking/router.py
- ต้องทำหลัง: T-02
- เสร็จเมื่อ: API ค้นช่างว่างและล็อกช่วงเวลาให้ผลเป็น slot ที่ว่างจริง พร้อม holdExpiresAt ตาม 10 นาที
- สถานะ: พร้อมทำ

### T-04 สร้างงานและชำระมัดจำผ่าน gateway
- รองรับ: REQ-FN-008, REQ-FN-041, REQ-IF-001, REQ-CON-003
- ตรวจด้วย: AC-07-01, AC-07-04, AC-07-05, AC-07-06
- ไฟล์ที่แตะ: backend/app/payments/gateway.py, backend/app/payments/service.py, backend/app/booking/service.py, backend/app/booking/router.py
- ต้องทำหลัง: T-03
- เสร็จเมื่อ: สร้างงานยืนยันแล้วได้ status ที่ถูกต้อง และ authorize/capture ผ่าน gateway interface เดิมตามสัญญา
- สถานะ: พร้อมทำ

### T-05 status inquiry เมื่อ gateway ไม่ตอบ
- รองรับ: REQ-IF-005, REQ-IF-001
- ตรวจด้วย: AC-07-05, AC-07-06, AC-07-07
- ไฟล์ที่แตะ: backend/app/payments/service.py, backend/app/payments/gateway.py, backend/tests/test_payment.py
- ต้องทำหลัง: T-04
- เสร็จเมื่อ: หาก gateway ไม่ตอบ จนถึง timeout จะทำ inquiry ด้วย reference เดิมและอัปเดตสถานะงานอย่างถูกต้อง
- สถานะ: รอ Q-19

### T-06 จัดการคืนมัดจำตาม BR-01
- รองรับ: REQ-BR-001, BR-01, MD-STM-01
- ตรวจด้วย: AC-07-10
- ไฟล์ที่แตะ: backend/app/refund/rules.py, backend/app/refund/service.py, backend/app/refund/router.py, backend/tests/test_refund.py
- ต้องทำหลัง: T-04
- เสร็จเมื่อ: คืนมัดจำแสดงผลตามรหัสกฎ R1, R2, R4, R5, R6 และแยกกรณี R3 ที่ยังรอคำตอบอย่างชัดเจน
- สถานะ: รอ Q-14

### T-07 แจ้งลูกค้าและช่างหลังสร้างงาน
- รองรับ: REQ-FN-012, IF-02
- ตรวจด้วย: AC-07-03
- ไฟล์ที่แตะ: backend/app/notify/queue.py, backend/app/booking/service.py, backend/app/main.py
- ต้องทำหลัง: T-04
- เสร็จเมื่อ: กระบวนการแจ้งเกิดภายใน 60 วินาทีหลังสร้างงาน และคิวสามารถอ่านได้สำหรับการทดสอบ
- สถานะ: พร้อมทำ

### T-08 ซ่อนเบอร์โทรและจำกัดมุมมองช่าง
- รองรับ: REQ-SEC-004, REQ-PRV-002
- ตรวจด้วย: AC-07-09
- ไฟล์ที่แตะ: backend/app/privacy.py, backend/app/booking/router.py, backend/app/booking/service.py, backend/tests/test_privacy.py
- ต้องทำหลัง: T-04
- เสร็จเมื่อ: ช่างเห็นเบอร์ลูกค้าเฉพาะช่วง 2 ชั่วโมงก่อนนัดและ log ไม่เก็บเบอร์โทร
- สถานะ: พร้อมทำ

### T-09 ทดสอบโหลดและประสิทธิภาพ REQ-QA-003
- รองรับ: REQ-QA-003
- ตรวจด้วย: AC-07-08
- ไฟล์ที่แตะ: backend/tests/test_load_scaled.py
- ต้องทำหลัง: T-03
- เสร็จเมื่อ: ทดสอบแบบย่อส่วนสำหรับผู้ใช้ 500 คนพร้อมกันและบันทึกผลว่าควรวัดอย่างไรในสภาพแวดล้อมของนักศึกษา
- สถานะ: พร้อมทำ

### T-10 หน้าจอเลือกช่างและยืนยันการจอง
- รองรับ: REQ-FN-008, REQ-FN-041, AC-07-04
- ตรวจด้วย: AC-07-04 (หน้าจอ)
- ไฟล์ที่แตะ: frontend/src/pages/TechnicianPicker.jsx, frontend/src/pages/ConfirmBooking.jsx, frontend/src/pages/BookingResult.jsx, frontend/src/api/client.js, frontend/src/__tests__/AC-07-04.test.jsx
- ต้องทำหลัง: ไม่มี
- เสร็จเมื่อ: หน้าเลือกช่าง/ยืนยันงานแสดงข้อมูลที่ถูกต้องและผ่าน test ของ AC-07-04
- สถานะ: พร้อมทำ

### T-11 ต่อหน้าจอกับ API จริง
- รองรับ: REQ-FN-008, REQ-FN-012, REQ-IF-001
- ตรวจด้วย: ไม่มี AC ตรง ๆ เป็นการยืนยัน integration แบบ end-to-end
- ไฟล์ที่แตะ: frontend/vite.config.js, backend/seed_demo.py
- ต้องทำหลัง: T-03, T-04, T-05, T-10
- เสร็จเมื่อ: เปิด frontend + backend แล้วจองงานได้จนเห็นหน้าผลการจองและสถานะงานที่ถูกต้อง
- สถานะ: พร้อมทำ

## ตารางตรวจความครบ

| AC ID | task ที่ตรวจ AC นี้ |
|---|---|
| AC-07-01 | T-03, T-04 |
| AC-07-02 | T-03 |
| AC-07-03 | T-07 |
| AC-07-04 | T-03, T-04, T-10 |
| AC-07-05 | T-04, T-05 |
| AC-07-06 | T-04, T-05 |
| AC-07-07 | T-05 |
| AC-07-08 | T-09 |
| AC-07-09 | T-08 |
| AC-07-10 | T-06 |

| Constraint ID | task ที่ทำให้เป็นจริง |
|---|---|
| REQ-CON-001 | T-01 |
| REQ-CON-003 | T-04 |
| REQ-IF-001 | T-04, T-05 |
| REQ-IF-005 | T-05 |
| IF-01 | T-04, T-05 |
| IF-02 | T-07 |
| IF-03 | T-03 |
| REQ-SEC-004 | T-08 |
| REQ-PRV-002 | T-01, T-02, T-08 |
| REQ-DAT-002 | T-02 |

## สิ่งที่ยังไม่ทำ

- Q-14: BR-01 R3 ต้องคืนเงินเท่าไรเมื่อลูกค้ายกเลิกขณะช่างเดินทาง — T-06 รอคำตอบและต้องไม่เดา
- Q-19: gateway-timeout ตามสัญญาคือกี่วินาที — T-05 รอคำตอบและใช้อีกตัวเลขชั่วคราวเท่านั้น
- Q-23: ถ้าลูกค้าปิดเบราว์เซอร์ระหว่างล็อก ใครปลดล็อก — T-03 รอคำตอบเพื่อยืนยัน mechanism ที่ใช่
- Q-18: ระยะล็อก 10 นาที ต้องยืนยันเป็นลายลักษณ์อักษรหรือไม่ — T-03/T-04 ต้องคอยสอดคล้องกับค่าที่ตอบกลับ
