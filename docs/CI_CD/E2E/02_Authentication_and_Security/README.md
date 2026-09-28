# 🔐 Journey 2: ระบบยืนยันตัวตน & บัญชีผู้ใช้ครบวงจร (Complete Auth & Account Flows)

> **ไดเรกทอรีชุดทดสอบ:** [`Code_devops/tcg-store-frontend/cypress/e2e/auth/`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/e2e/auth/)  
> **หมวดหมู่:** `E2E Testing / Core User Journey 2 (Auth & Account)`  
> **กลุ่มผู้ใช้งาน (Actors):** Guest, ผู้ใช้ใหม่ (New User), ผู้ใช้ที่ลืมรหัสผ่าน, สมาชิกทั่วไป (`buyer@mythix.com`)

---

## 📌 1. วัตถุประสงค์ (Objective)
ทดสอบระบบรักษาความปลอดภัย การยืนยันตัวตน และการบริหารจัดการบัญชีผู้ใช้งาน (Authentication & Account Management) แบบครบวงจร 100%:
1. **Login & Route Security:** ตรวจสอบแบบฟอร์มล็อกอิน, การซ่อน/แสดงรหัสผ่าน, และ Route Guard บล็อก Guest ไม่ให้เข้า `/admin`, `/workspace`, `/profile`
2. **Registration & Sign-Up:** ตรวจสอบการสมัครสมาชิกใหม่, เช็คลิสต์ความปลอดภัยรหัสผ่าน (8 ตัว, พิมพ์ใหญ่, พิมพ์เล็ก, ตัวเลข, สัญลักษณ์), และการตรวจจับรหัสผ่านไม่ตรงกัน
3. **Forgot & Reset Password:** ตรวจสอบกระบวนการขอรีเซ็ตรหัสผ่าน, การป้องกันไม่ให้เข้า `/reset-password` ตรงๆ โดยไม่มีสิทธิ์, และหน้าผลลัพธ์สำเร็จ
4. **User Profile & Contact Information:** ตรวจสอบการดูข้อมูลส่วนตัว, การแก้ไขชื่อ-นามสกุล, และการอัปเดตช่องทางติดต่อ (เบอร์โทร, Line ID, Facebook)

---

## 📁 2. โครงสร้างไฟล์ชุดทดสอบในโฟลเดอร์ `cypress/e2e/auth/`

```text
cypress/e2e/auth/
├── 01_login_and_security.cy.ts            # การล็อกอิน, Validation และการป้องกัน Route
├── 02_registration_and_signup.cy.ts       # การสมัครสมาชิกใหม่ และกฎความปลอดภัยรหัสผ่าน
├── 03_forgot_and_reset_password.cy.ts     # การกู้คืนรหัสผ่าน และหน้ารีเซ็ต
└── 04_user_profile_and_contact.cy.ts      # การดูและแก้ไขข้อมูลส่วนตัว / ช่องทางติดต่อ
```

---

## 🧪 3. รายละเอียด Test Scenarios แต่ละ Flow

### 🔹 Flow 2.1: Login, Form Validations & Route Protection (`01_login_and_security.cy.ts`)
* **TC-E2E-AUTH-01:** แสดงผลฟอร์มล็อกอิน ฟิลด์ `#email`, `#password`, ปุ่ม Submit และลิงก์ไปยัง `/forgot-password`, `/register`
* **TC-E2E-AUTH-02:** การสลับมุมมองรหัสผ่าน (Toggle Password Visibility) ระหว่าง `type="password"` และ `type="text"` ด้วยไอคอนรูปตา
* **TC-E2E-AUTH-03:** การดักจับ Validation เมื่อผู้ใช้กด Submit โดยไม่กรอกข้อมูล (URL ยังคงอยู่ที่ `/login`)
* **TC-E2E-AUTH-04:** **Route Guard (Admin):** Guest พยายามเข้า `/admin` แล้วระบบต้อง Redirect กลับไปที่ `/login`
* **TC-E2E-AUTH-05:** **Route Guard (Seller):** Guest พยายามเข้า `/workspace` แล้วระบบต้อง Redirect กลับไปที่ `/login`
* **TC-E2E-AUTH-06:** **Route Guard (Profile):** Guest พยายามเข้า `/profile` แล้วระบบต้อง Redirect กลับไปที่ `/login`

---

### 🔹 Flow 2.2: New User Registration & Sign-Up Validation (`02_registration_and_signup.cy.ts`)
* **TC-E2E-SIGNUP-01:** เรนเดอร์แบบฟอร์มสมัครสมาชิก (`/register`) ครบถ้วน (Name, Email, Password, Confirm Password)
* **TC-E2E-SIGNUP-02:** เมื่อคลิกโฟกัสที่ช่อง Password จะมีกล่อง Checklist ข้อกำหนดความปลอดภัย 5 ข้อ ปรากฏขึ้นมาแบบ Interactive:
  * At least 8 characters
  * At least one uppercase letter
  * At least one lowercase letter
  * At least one number
  * At least one special character
* **TC-E2E-SIGNUP-03:** ตรวจจับเมื่อ Confirm Password ไม่ตรงกับ Password ➔ แสดงข้อความ *"Passwords do not match"*
* **TC-E2E-SIGNUP-04:** ตรวจจับเมื่อ Password ไม่ผ่านเกณฑ์ความปลอดภัย ➔ แสดงข้อความ *"Password does not meet the requirements"*
* **TC-E2E-SIGNUP-05:** ลิงก์ *"Sign in"* นำทางผู้ใช้กลับไปหน้า `/login` ได้ถูกต้อง

---

### 🔹 Flow 2.3: Forgot & Reset Password Flow (`03_forgot_and_reset_password.cy.ts`)
* **TC-E2E-RESET-01:** เรนเดอร์หน้า `/forgot-password` พร้อมช่องใส่อีเมลและปุ่ม *"Continue"*
* **TC-E2E-RESET-02:** ป้องกันการเข้าถึงโดยพลการ: หากผู้ใช้พยายามเข้าหน้า `/reset-password` ตรงๆ โดยไม่ได้เริ่มจากหน้า Forgot Password ระบบจะ Redirect กลับไปที่ `/forgot-password` ทันที
* **TC-E2E-RESET-03:** เมื่อมี Session การขอรีเซ็ต (เช่น `mythix_demo_reset_email`) หน้า `/reset-password` จะเรนเดอร์ฟอร์ม New Password และ Confirm Password
* **TC-E2E-RESET-04:** ตรวจจับเมื่อรหัสผ่านใหม่สองช่องไม่ตรงกัน
* **TC-E2E-RESET-05:** หน้า `/reset-success` แสดงข้อความรีเซ็ตสำเร็จ พร้อมปุ่ม *"Sign in"* นำทางไปสู่หน้า `/login`

---

### 🔹 Flow 2.4: User Profile & Contact Information Management (`04_user_profile_and_contact.cy.ts`)
* **TC-E2E-PROF-01:** ผู้ใช้ที่มี Session เข้าหน้า `/profile` จะเห็นกล่องข้อมูล 2 ส่วน: **Personal Information** และ **Contact Information**
* **TC-E2E-PROF-02:** คลิกปุ่ม *"Edit profile"* ➔ นำทางไปหน้า `/profile/edit` ➔ แสดงช่องแก้ไข Full Name ➔ ปุ่ม *"Back to profile"* นำทางกลับมาหน้าหลักได้
* **TC-E2E-PROF-03:** คลิกปุ่ม *"Edit contact"* ➔ นำทางไปหน้า `/profile/edit/contact` ➔ แสดงช่องกรอก Phone, Line ID, Facebook ➔ ปุ่ม *"Back to profile"* นำทางกลับมาหน้าหลักได้
* **TC-E2E-PROF-04:** ป้องกัน Guest: หากไม่มี Session ล็อกอินแล้วพยายามเข้า `/profile` ระบบจะ Redirect ไปที่ `/login` ทันที

---

## ⚡ 4. Custom Commands พิเศษสำหรับ Auth & Account

เราได้เพิ่มคำสั่ง Custom Commands ลงใน [`cypress/support/commands.ts`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/support/commands.ts) เพื่อให้เขียนเทสได้ง่ายและกระชับ:

| Custom Command | ตัวอย่างการเรียกใช้งาน | ประโยชน์ |
| :--- | :--- | :--- |
| `cy.loginByUI(email, password)` | `cy.loginByUI('buyer@mythix.com', 'password123')` | จำลองการกรอกฟอร์มล็อกอินจริงผ่านหน้าจอเบราว์เซอร์ |
| `cy.registerNewUser(name, email, pwd)` | `cy.registerNewUser('Ash', 'ash@mythix.com', 'Pass123!')` | จำลองการกรอกฟอร์มสมัครสมาชิกใหม่ครบ 4 ฟิลด์ |
| `cy.requestPasswordReset(email)` | `cy.requestPasswordReset('buyer@mythix.com')` | จำลองการกรอกอีเมลเพื่อขอรีเซ็ตรหัสผ่าน |
| `cy.updateProfileName(name)` | `cy.updateProfileName('Ash Ketchum')` | เข้าหน้า `/profile/edit` แก้ไขชื่อและบันทึกอัตโนมัติ |
| `cy.updateProfileContact({ phone, line })`| `cy.updateProfileContact({ phone: '0812345678' })` | เข้าหน้า `/profile/edit/contact` อัปเดตข้อมูลติดต่อ |

---

## 💻 5. คำสั่งการรันเฉพาะกลุ่ม Auth & Account Flows

สามารถสั่งรันชุดทดสอบ Auth & Account ทั้งหมดผ่านคำสั่งเฉพาะได้ทันที:

### 🤖 1. รันอัตโนมัติผ่าน Command Line (Headless Mode):
```bash
cd Code_devops/tcg-store-frontend
npm run test:e2e:auth
```

### 🎯 2. รันแบบ Interactive GUI (สำหรับดูหน้าจอคลิกสดๆ):
```bash
cd Code_devops/tcg-store-frontend
npm run test:e2e:auth:open
```
> ระบบจะเปิดหน้าต่าง Cypress ที่กรองเฉพาะไฟล์ในโฟลเดอร์ `cypress/e2e/auth/` ให้เลือกทดสอบได้ทันที
