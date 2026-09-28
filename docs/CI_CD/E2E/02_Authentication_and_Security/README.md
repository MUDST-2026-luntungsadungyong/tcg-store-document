# 🔐 Journey 2: ระบบยืนยันตัวตนและการปกป้องสิทธิ์ (Authentication & Route Security)

> **อ้างอิง Spec File:** [`Code_devops/tcg-store-frontend/cypress/e2e/02_auth_and_protected_routes.cy.ts`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/e2e/02_auth_and_protected_routes.cy.ts)  
> **หมวดหมู่:** `E2E Testing / Core User Journey 2`  
> **กลุ่มผู้ใช้งาน (Actor):** Guest / ผู้ใช้ที่ไม่ได้รับอนุญาต (Unauthorized User)

---

## 📌 1. วัตถุประสงค์ (Objective)
ทดสอบระบบรักษาความปลอดภัยด่านหน้าของแอปพลิเคชัน (Frontend Security & Route Protection):
1. ตรวจสอบการเรนเดอร์แบบฟอร์มเข้าสู่ระบบ (`/login`)
2. ตรวจสอบ Validation เบื้องต้นเมื่อผู้ใช้กด Submit โดยไม่กรอกข้อมูล
3. ตรวจสอบกลไก **Route Guard (ProtectedRoute)** ว่าสามารถป้องกัน Guest ไม่ให้แอบเข้าถึงหน้าหลังบ้านของผู้ดูแลระบบ (`/admin`) และหน้าจัดการร้านของผู้ขาย (`/workspace`) ได้อย่างถูกต้อง

---

## 🧪 2. รายละเอียด Test Scenarios

### 🔹 TC-E2E-AUTH-01: การแสดงผลฟอร์มล็อกอิน (Login Form Elements)
* **Pre-condition:** ผู้ใช้ยังไม่ได้เข้าสู่ระบบ
* **Action:** `cy.visit('/login')`
* **Assertions:**
  * มี Input ฟิลด์ `email` (`input[type="email"]`) ปรากฏบนหน้าจอ
  * มี Input ฟิลด์ `password` (`input[type="password"]`) ปรากฏบนหน้าจอ
  * มีปุ่ม `button[type="submit"]` สำหรับกดเข้าสู่ระบบ

### 🔹 TC-E2E-AUTH-02: การตรวจจับข้อมูลว่างเปล่า (Empty Form Validation)
* **Pre-condition:** อยู่ที่หน้า `/login`
* **Action:** กดปุ่ม Submit ทันทีโดยไม่กรอก Email และ Password
* **Assertions:**
  * ฟอร์มไม่ยอมให้ผ่าน และยังคงอยู่ที่หน้า `/login`
  * มีข้อความเตือน หรือ Browser Built-in Validation ป้องกันการส่งคำขอ

### 🔹 TC-E2E-AUTH-03: การปกป้องเส้นทางผู้ดูแลระบบ (Admin Route Protection)
* **Pre-condition:** ผู้ใช้เป็น Guest (ไม่มี Token ใน Storage)
* **Action:** พยายามเข้าหน้า `/admin` โดยตรง (`cy.visit('/admin')`)
* **Assertions:**
  * `ProtectedRoute` ทำงานทันที
  * URL ถูก Redirect กลับมาที่ `/login` เสมอ ห้ามเห็นข้อมูลในแดชบอร์ด

### 🔹 TC-E2E-AUTH-04: การปกป้องเส้นทางผู้ขาย (Seller Workspace Protection)
* **Pre-condition:** ผู้ใช้เป็น Guest (ไม่มี Token ใน Storage)
* **Action:** พยายามเข้าหน้า `/workspace` โดยตรง (`cy.visit('/workspace')`)
* **Assertions:**
  * `ProtectedRoute` ทำงานทันที
  * URL ถูก Redirect กลับมาที่ `/login` เสมอ

---

## 📋 3. ข้อมูลทางเทคนิค (Technical Details & Storage Keys)

| ฟังก์ชัน / Component | พฤติกรรมที่คาดหวัง |
| :--- | :--- |
| **`ProtectedRoute.tsx`** | ตรวจสอบ `useAuthStore.getState().user` หากเป็น `null` จะสั่ง `<Navigate to="/login" replace />` |
| **Local Storage Token** | คีย์ `token` ต้องไม่มีอยู่ใน Storage ก่อนเริ่มการทดสอบ |
| **Post-Auth Navigation** | เมื่อล็อกอินสำเร็จต้องจำ URL ปลายทางเดิมที่ผู้ใช้พยายามเข้าได้ |

---

## 🔮 4. แผนการต่อขยายในอนาคต (Future Expansion)
* [ ] การทดสอบ Flow สมัครสมาชิกใหม่ (Sign Up User Journey)
* [ ] การทดสอบกรณี Login ด้วยรหัสผ่านผิดแล้วแสดง Error Toast สีแดง
* [ ] การทดสอบ Forget Password ➔ ส่ง Email OTP / Reset Password Link
* [ ] การทดสอบ Role Separation: User ธรรมดาพยายามเข้า `/admin` ต้องเจอหน้า 403 Forbidden
