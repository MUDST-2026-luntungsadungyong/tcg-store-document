# Core User Journey 5: Admin Platform

เอกสารนี้อธิบายการทำงานของ E2E Test สำหรับ Admin Platform ตามเอกสาร Flow ของผู้ใช้ (Platform Admin)

## โฟลเดอร์
`tcg-store-frontend/cypress/e2e/admin/`

## คำสั่งทดสอบ (NPM Script)
สามารถรัน E2E ของ Admin Platform ได้ด้วยคำสั่ง:
```bash
# รันชุด Admin Platform ทั้งหมด (33 tests)
npm run test:e2e:admin

# รันเฉพาะสเปก Order Logs Monitoring (US-12-5)
npm run test:e2e:admin-orders
```

---

## สรุป E2E Scripts ที่ครอบคลุม

### 1. `01_admin_access_and_authorization.cy.ts` (อ้างอิง US-12)
**จุดประสงค์:** ทดสอบระบบ Role Guard ป้องกันไม่ให้ผู้ใช้ทั่วไปเข้าถึงระบบหลังบ้าน
- **Flow ที่ทดสอบ:**
  - ตรวจสอบว่าถ้าเข้า `/admin` โดยยังไม่ Login จะโดนดีดไปหน้า Login
  - ตรวจสอบว่าถ้า Login ด้วย Role `USER` ธรรมดา แล้วพยายามเข้า `/admin` จะโดนดีดไปหน้า Home
  - ตรวจสอบว่าถ้า Login ด้วยสิทธิ์ `ADMIN` จะสามารถเข้าถึง Dashboard และเห็น Layout ของ Admin ได้
  - ตรวจสอบการ Login ด้วยอีเมลแอดมินแต่รหัสผ่านผิด จะต้องขึ้น Error
- **Custom Commands ที่ใช้:**
  - `cy.loginByUI()`
  - `cy.loginAsAdmin()`

### 2. `02_admin_dashboard.cy.ts` (อ้างอิง US-12-1)
**จุดประสงค์:** ทดสอบการแสดงผลหน้า Overview ของผู้ดูแลระบบ (Admin Dashboard)
- **Flow ที่ทดสอบ:**
  - ตรวจสอบการแสดง Skeleton Loading ขณะกำลังดึงข้อมูล Dashboard ผ่าน `GET /api/admin/dashboard`
  - ตรวจสอบกรณีที่ข้อมูลโหลดสำเร็จและ**ไม่มี** Application ค้างอยู่ (แสดง Empty State)
  - ตรวจสอบกรณีที่ข้อมูลโหลดสำเร็จและ**มี** Application ค้างอยู่ (แสดงจำนวน และมีลิงก์กดไปหน้าคิว)
  - ตรวจสอบกรณีที่เกิด API Error จะต้องแสดงข้อความ "Could not load dashboard data. Refresh to try again." และสามารถกด Refresh เพื่อโหลดใหม่ได้

### 3. `03_seller_application_queue.cy.ts` (อ้างอิง US-12-2 & US-12-6)
**จุดประสงค์:** ทดสอบตารางคิวจัดการคำขอเป็นผู้ขาย และการอนุมัติ/ปฏิเสธคำขอ
- **Flow ที่ทดสอบ:**
  - ตรวจสอบการแสดงผล Skeleton Loading ขณะรอข้อมูล
  - ตรวจสอบการแสดง Error เมื่อ `GET /api/admin/seller-applications` ล้มเหลว และการกด Retry
  - ตรวจสอบการแสดงผลตารางที่มีผู้ขอสมัครเป็นผู้ขาย (แสดงข้อมูลชื่อ, อีเมล, ชื่อร้านค้า, และปุ่ม Approve / Reject)
  - ตรวจสอบกรณีไม่มีคนสมัครเลย (แสดง Empty state)
  - ตรวจสอบการกด Approve เพื่ออนุมัติคำขอร้านค้า
  - ตรวจสอบการกด Reject พร้อมกรอกเหตุผลการปฏิเสธใน Modal

### 4. `04_seller_update_product.cy.ts` (อ้างอิง US-05-3)
**จุดประสงค์:** ทดสอบการแก้ไขข้อมูลสินค้าที่วางขายของผู้ขาย
- **Flow ที่ทดสอบ:**
  - เข้าสู่หน้าจัดการสินค้า แก้ไขราคาและสต็อกสำเร็จ
  - ตรวจสอบการป้องกันการบันทึกเมื่อกรอกข้อมูลไม่ครบถ้วน
  - ตรวจสอบการยกเลิกการแก้ไขและคืนค่าเดิม

### 5. `05_admin_users_management.cy.ts` (อ้างอิง US-12-3)
**จุดประสงค์:** ทดสอบการจัดการบัญชีผู้ดูแลระบบ (Admin Users Management)
- **Flow ที่ทดสอบ:**
  - ตรวจสอบการแสดงรายชื่อ Administrator ทั้งหมดในระบบ
  - ตรวจสอบการกดปุ่ม "Add admin" กรอกข้อมูล และสร้างผู้ดูแลระบบคนใหม่
  - ตรวจสอบการกดลบบัญชีผู้ดูแลระบบ พร้อมการยืนยันใน Modal

### 6. `06_admin_audit_logs.cy.ts` (อ้างอิง US-12-4)
**จุดประสงค์:** ทดสอบการตรวจสอบประวัติการดำเนินงานและความปลอดภัย (Audit Logs)
- **Flow ที่ทดสอบ:**
  - ตรวจสอบการแสดงรายการ Audit Log ในตาราง
  - ตรวจสอบการคลิกที่แถวรายการเพื่อเปิดดูรายละเอียดใน Modal (Actor, Action, Target, IP Address, Timestamp)

### 7. `07_admin_orders_monitoring.cy.ts` (อ้างอิง US-12-5)
**จุดประสงค์:** ทดสอบระบบติดตามคำสั่งซื้อทั้งหมดทั่วทั้งระบบ (Platform-wide Order Logs Monitoring)
- **Route:** `/admin/orders`
- **Component:** `AdminOrderLogsPage.tsx`
- **Flow ที่ทดสอบ (12 Scenarios — ผ่าน 100%):**
  1. **Route Protection & Access Control:**
     - ผู้ใช้ Guest (ยังไม่ล็อกอิน) เข้า `/admin/orders` ถูก Redirect ไปยัง `/login`
     - ผู้ใช้ทั่วไป (Role `USER`/`CUSTOMER`) เข้า `/admin/orders` ถูก Guard ดีดกลับไปหน้าแรก `/`
     - แอดมิน (Role `ADMIN`) เข้าถึง `/admin/orders` ได้อย่างถูกต้อง แสดง Header "Order logs", Subtitle "Platform administration" และ Active Sidebar Link
  2. **Table Presentation & Loading States:**
     - แสดง Loading Skeleton ขณะดึงข้อมูลคำสั่งซื้อ
     - แสดงตาราง Desktop พร้อมคอลัมน์ Order, Buyer, Seller, Status, Date, Amount
     - แสดงตัวเลขออเดอร์รวม "5 orders found" พร้อมสถานะและราคาฟอร์แมตเงินบาท (`฿...`)
  3. **Search & Filter Capabilities:**
     - ฟิลเตอร์สถานะออเดอร์ด้วย Status Pills (`PENDING`, `PAID`, `FULFILLED`, `CANCELLED`) รองรับ Multi-select และ Toggle deselect
     - ค้นหาด้วย Order Number, Buyer Name, และ Seller Name พร้อมปุ่มกากบาท `X` เคลียร์ข้อความ
     - เปิด-ปิดตัวเลือกช่วงเวลา (Date Range Picker Popover) พร้อมปุ่ม Clear และ Apply
  4. **Order Details Modal:**
     - คลิกแถวออเดอร์เปิด Modal รายละเอียดออเดอร์ (`Order details`)
     - แสดง Order ID, วันที่/เวลา, ข้อมูลผู้ซื้อ (ชื่อ, อีเมล)
     - แสดงสรุปการเงิน (ยอดรวม, ค่าธรรมเนียมแพลตฟอร์ม, ยอดสุทธิผู้ขาย)
     - แสดงรายการสินค้า (Item Name, Card ID, Seller Name, Shop, Qty, Subtotal)
     - กดปุ่มปิด `X` เพื่อปิด Modal และนำออกจาก DOM
  5. **Pagination & Refresh Handling:**
     - จัดการเปลี่ยนหน้า Pagination (Page 1 of 2 ➔ Page 2 of 2) และสลับสถานะปุ่ม Previous / Next
     - กดปุ่ม "Refresh" เพื่อโหลดข้อมูลออเดอร์ใหม่แบบเรียลไทม์
     - แสดง Empty State "0 orders found" เมื่อไม่มีรายการออเดอร์
