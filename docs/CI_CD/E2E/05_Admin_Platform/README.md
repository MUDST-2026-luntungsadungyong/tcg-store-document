# Core User Journey 5: Admin Platform

เอกสารนี้อธิบายการทำงานของ E2E Test สำหรับ Admin Platform ตามเอกสาร Flow ของผู้ใช้ (Platform Admin)

## โฟลเดอร์
`Code_devops/tcg-store-frontend/cypress/e2e/admin/`

## คำสั่งทดสอบ (NPM Script)
สามารถรันเฉพาะ E2E ของ Admin Platform ได้ด้วยคำสั่ง:
```bash
npm run test:e2e:admin
```

## สรุป E2E Scripts ที่ครอบคลุม

### 1. `01_admin_access_and_authorization.cy.ts` (อ้างอิง US-11-1)
**จุดประสงค์:** ทดสอบระบบ Role Guard ป้องกันไม่ให้ผู้ใช้ทั่วไปเข้าถึงระบบหลังบ้าน
- **Flow ที่ทดสอบ:**
  - ตรวจสอบว่าถ้าเข้า `/admin` โดยยังไม่ Login จะโดนดีดไปหน้า Login
  - ตรวจสอบว่าถ้า Login ด้วย Role `USER` ธรรมดา แล้วพยายามเข้า `/admin` จะโดนดีดไปหน้า Home
  - ตรวจสอบว่าถ้า Login ด้วยสิทธิ์ `ADMIN` จะสามารถเข้าถึง Dashboard และเห็น Layout ของ Admin ได้
  - ตรวจสอบการ Login ด้วยอีเมลแอดมินแต่รหัสผ่านผิด จะต้องขึ้น Error
- **Custom Commands ที่ใช้:**
  - `cy.loginByUI()`
  - `cy.loginAsAdmin()`

### 2. `02_admin_dashboard.cy.ts` (อ้างอิง US-11-2)
**จุดประสงค์:** ทดสอบการแสดงผลหน้า Overview ของผู้ดูแลระบบ (Admin Dashboard)
- **Flow ที่ทดสอบ:**
  - ตรวจสอบการแสดง Skeleton Loading ขณะกำลังดึงข้อมูล Dashboard ผ่าน `GET /api/admin/dashboard`
  - ตรวจสอบกรณีที่ข้อมูลโหลดสำเร็จและ**ไม่มี** Application ค้างอยู่ (แสดง Empty State)
  - ตรวจสอบกรณีที่ข้อมูลโหลดสำเร็จและ**มี** Application ค้างอยู่ (แสดงจำนวน และมีลิงก์กดไปหน้าคิว)
  - ตรวจสอบกรณีที่เกิด API Error จะต้องแสดงข้อความ "Could not load dashboard data. Refresh to try again." และสามารถกด Refresh เพื่อโหลดใหม่ได้

### 3. `03_seller_application_queue.cy.ts` (อ้างอิง US-11-3)
**จุดประสงค์:** ทดสอบตารางคิวจัดการคำขอเป็นผู้ขาย
- **Flow ที่ทดสอบ:**
  - ตรวจสอบการแสดงผล Skeleton Loading ขณะรอข้อมูล
  - ตรวจสอบการแสดง Error เมื่อ `GET /api/admin/seller-applications` ล้มเหลว และการกด Retry
  - ตรวจสอบการแสดงผลตารางที่มีผู้ขอสมัครเป็นผู้ขาย (แสดงข้อมูลชื่อ, อีเมล, ชื่อร้านค้า, และปุ่ม Approve / Reject)
  - ตรวจสอบกรณีไม่มีคนสมัครเลย (แสดง Empty state)
