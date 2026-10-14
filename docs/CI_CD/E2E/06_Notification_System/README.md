# 🔔 Core User Journey 6: In-App Notification Center

> **อ้างอิง User Story:** `US-11-1: In-App Notifications Inbox & Status Alerts` และ `US-11-2: Notification Badge & Unread Count`  
> **ไฟล์ทดสอบ:** [`cypress/e2e/05_notifications_inbox.cy.ts`](file:///c:/Devops/tcg-store-frontend/cypress/e2e/05_notifications_inbox.cy.ts)  
> **หมวดหมู่:** `CI_CD / E2E`  
> **สถานะ:** 🟢 ผ่านการทดสอบครบ 14/14 เคส (100% Pass)

---

## 📌 1. ภาพรวมการทดสอบ (Overview)

เอกสารนี้อธิบายชุดทดสอบระดับ End-to-End (E2E) สำหรับระบบการแจ้งเตือนภายในแอปพลิเคชัน (In-App Notification Center) ซึ่งครอบคลุมทั้ง:
1. การแจ้งเตือนบน Navbar ด้วยไอคอนกระดิ่งและตัวเลขนับข้อความที่ยังไม่อ่าน (Dynamic Badge Counter)
2. กล่องข้อความแจ้งเตือน (Notification Inbox) พร้อมปุ่มนำทางไปยังเป้าหมายตามประเภทของการแจ้งเตือน (Deep-Link Navigation)
3. การจัดการสถานะแจ้งเตือน (Mark as read, Delete notification, Empty State)
4. การแบ่งหน้า (Pagination Navigation)
5. การรับมือกับข้อผิดพลาดของระบบและข้อจำกัดด้านสิทธิ์ (Error Recovery 503 & Session Expiration 401)

---

## 💻 2. คำสั่งทดสอบ (Execution Command)

สามารถรันชุดทดสอบ Notification System ได้อย่างสะดวกรวดเร็วผ่านคำสั่ง:

```bash
# ในโฟลเดอร์ tcg-store-frontend
npm run test:e2e:notifications
```

หรือสั่งผ่าน Cypress CLI โดยตรง:
```bash
npx cypress run --spec "cypress/e2e/05_notifications_inbox.cy.ts"
```

---

## 🧪 3. รายละเอียด Test Cases ที่ครอบคลุม (14 Scenarios)

### 🔹 หมวดที่ 1: Navbar Bell Icon & Unread Badges (US-11-2)
* **1.1 แสดง Badge นับจำนวนข้อความที่ยังไม่อ่าน:** ตรวจสอบว่า Navbar แสดงไอคอนกระดิ่งและ Badge ตัวเลขสีแดงตรงตามค่า Unread Count (เช่น `5`)
* **1.2 การแสดงป้ายกรณีข้อความเกิน 99 (`99+`):** ตรวจสอบว่าเมื่อมี Unread เกิน 99 รายการ (เช่น `120`) ระบบจะย่อแสดงผลเป็น `99+`
* **1.3 ซ่อน Badge เมื่อไม่มีข้อความค้างอ่าน:** ตรวจสอบว่าเมื่อ Unread Count เป็น `0` ไอคอนกระดิ่งยังคงแสดง แต่จะไม่มีป้ายตัวเลขสีแดงปรากฏ
* **1.4 Guest Redirection Guard:** ผู้ใช้ที่ยังไม่ได้ล็อกอิน (Guest) เมื่อคลิกไอคอนกระดิ่ง ระบบจะ Redirect ไปยังหน้า `/login` ทันที

### 🔹 หมวดที่ 2: Card Types & Deep-Link Navigations (US-11-1)
* **2.1 `ORDER_RESERVED` (แจ้งเตือนคำสั่งจอง):** แสดงการจองการ์ดสำหรับผู้ขาย พร้อมปุ่ม *"View order"* ที่เชื่อมโยงไปยังหน้ารายการออเดอร์ผู้ขาย
* **2.2 `ORDER_STATUS_CHANGED` (ออเดอร์จัดส่งแล้ว):** แสดงสถานะการส่งของ พร้อมปุ่ม *"View order"* นำทางผู้ซื้อไปยังหน้า `/orders`
* **2.3 `SELLER_APPLICATION_SUBMITTED` (แจ้งเตือนแอดมิน):** แสดงคำขอเปิดร้านของผู้ขาย พร้อมปุ่ม *"Review application"*
* **2.4 `SELLER_APPLICATION_APPROVED` (อนุมัติเปิดร้าน):** แสดงความยินดี พร้อมปุ่ม *"View profile"* นำทางไปยังหน้า `/profile`

### 🔹 หมวดที่ 3: Read, Delete & Dynamic Lifecycle (US-11-1 & US-11-2)
* **3.1 Mark as Read (`PATCH /api/notifications/:id/read`):** กดปุ่มอ่านแล้ว จุดสีฟ้า Unread เปลี่ยนเป็น Read และปุ่ม Mark read หายไป
* **3.2 Delete Notification (`DELETE /api/notifications/:id`):** กดปุ่มลบการแจ้งเตือน รายการถูกถอดออกจากหน้าจอ
* **3.3 Empty State Verification:** เมื่อลบการแจ้งเตือนหมดทุกรายการ หน้าจอจะแสดง Empty State: *"You’re all caught up"* และ *"New reservation and order updates will appear here."*

### 🔹 หมวดที่ 4: Pagination Support (US-11-1)
* **4.1 ระบบแบ่งหน้า:** จำลองรายการแจ้งเตือน 21 รายการ (หน้าละ 20 รายการ) ตรวจสอบปุ่ม Previous/Next และการคลิกเปลี่ยนไปหน้า 2 อย่างถูกต้อง

### 🔹 หมวดที่ 5: Error Handling & Authentication Guard (US-11-1)
* **5.1 HTTP 503 Server Unavailable & Retry:** เมื่อ API โหลดไม่สำเร็จ แสดงกล่องเตือน *"We could not load your notifications. Please try again."* และเมื่อกดปุ่ม *"Try again"* สามารถกู้คืนและโหลดข้อมูลสำเร็จ
* **5.2 HTTP 401 Session Expired:** เมื่อ Token หมดอายุ แสดงกล่องเตือน *"Your session has expired. Sign in to view your notifications."* และเมื่อกดปุ่ม *"Sign in"* จะนำทางไปหน้า `/login`

### 🔹 หมวดที่ 6: Visual & UI Theme Compliance
* **6.1 White Bubble Notification Cards:** ตรวจสอบสไตล์การแสดงผลการ์ดแจ้งเตือนว่าเป็นสีพื้นหลังสีขาว (`rgb(255, 255, 255)`) ถูกต้องตาม Design System
* **6.2 Bubble Layout Compatibility:** ตรวจสอบความเข้ากันได้ของการแสดงผลร่วมกับการ์ดในหน้ารายละเอียดสินค้า

---

## 📊 4. สรุปผลการรันจริง (Verification Results)

```text
       Spec                                              Tests  Passing  Failing  Pending  Skipped  
  ┌────────────────────────────────────────────────────────────────────────────────────────────────┐
  │ √  05_notifications_inbox.cy.ts             00:26       14       14        -        -        - │
  └────────────────────────────────────────────────────────────────────────────────────────────────┘
    √  All specs passed!                        00:26       14       14        -        -        -  
```
