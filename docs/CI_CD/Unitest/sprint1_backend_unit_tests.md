# 🧪 รายงานสรุปผลการทดสอบ Unit Test (Sprint 1)
### Mythix TCG Platform — Backend Service Testing (Java 21 / Spring Boot 3)

> **เอกสารอ้างอิง Jira Task:**
> * `SCRUM-36`: [Delivery] Final Validation for Sprint 1 (Runnable Code & CI) — *"ยืนยันว่ามีการเขียน Unit Test อย่างน้อยใน Backend Service หลัก"*
> * `SCRUM-9`: [Backend] US-01-1: Registration & Login System API — *"มี Unit Test (JUnit 5 + Mockito) ทดสอบ Service Method สำหรับ Login & Register"*
> * `SCRUM-6` / `SCRUM-16`: [Backend] User Profile & Store Profile Management API
> * `SCRUM-26`: [Backend] Seller Order & Dashboard Management API
> * `SCRUM-20`: [DevOps] DO-03: Automated Unit Test CI Pipeline

---

## 📌 1. ภาพรวมและกลยุทธ์การทดสอบ (Testing Strategy)

ในการส่งมอบงาน **Sprint 1** ทีม Backend ได้นำเทคนิค **Isolated Unit Testing** โดยใช้ **JUnit 5 + Mockito** มาใช้ทดสอบสมองและตรรกะทางธุรกิจ (Business Logic) ในชั้น **Service Layer** โดยมีจุดเด่นดังนี้:
1. **ความเร็วสูง (Fast Execution):** ใช้ Mockito จำลองการทำงานของ Database Repository ทำให้รันเทสทั้ง 25 เคสเสร็จสิ้นภายในไม่กี่ร้อยมิลลิวินาที โดยไม่ต้องรอการเชื่อมต่อฐานข้อมูลภายนอก
2. **ความแม่นยำ (Deterministic):** ทดสอบแยกส่วนเด็ดขาดตามสูตร **Arrange - Act - Assert (AAA)** ควบคุมผลลัพธ์และเงื่อนไขจำลองได้ 100%
3. **ครอบคลุมทั้ง Happy Path และ Edge Cases:** ตรวจสอบทั้งกรณีที่ข้อมูลถูกต้อง และกรณีที่มี Error เช่น อีเมลซ้ำ, รหัสผ่านผิด, สินค้าไม่พบ, ผู้ใช้ไม่มีสิทธิ์

---

## 📋 2. รายละเอียด Test Suites และ Test Cases ทั้งหมดใน Sprint 1

### 🔐 2.1 `AuthServiceUnitTest` (ระบบยืนยันตัวตนและความปลอดภัย)
*ไฟล์ทดสอบ:* `com.mythix.tcg.auth.application.AuthServiceUnitTest`  
*จำนวนเคส:* **7 Test Cases**

| ลำดับ | ชื่อ Test Case | วัตถุประสงค์และเงื่อนไขการทดสอบ | ผลลัพธ์ที่คาดหวัง |
|:---:|---|---|---|
| 1 | `register_success` | สมัครสมาชิกด้วยชื่อ อีเมลใหม่ และรหัสผ่านที่ถูกต้อง | บันทึกสำเร็จ, รหัสผ่านถูกเข้ารหัสด้วย BCrypt, ส่งคืน JWT Token และข้อมูล User |
| 2 | `register_duplicateEmail_throwsException` | สมัครสมาชิกด้วยอีเมลที่มีอยู่ในระบบแล้ว | โยน `DuplicateEmailException` ทันที และไม่บันทึกลงฐานข้อมูล |
| 3 | `login_success` | เข้าสู่ระบบด้วยอีเมลและรหัสผ่านที่ถูกต้อง | เข้าสู่ระบบสำเร็จ และส่งคืน JWT Token |
| 4 | `login_wrongPassword_throwsException` | กรอกรหัสผ่านผิด | โยน `InvalidCredentialsException` ปฏิเสธการเข้าสู่ระบบ |
| 5 | `login_emailNotFound_throwsException` | กรอกอีเมลที่ไม่มีอยู่ในระบบ | โยน `InvalidCredentialsException` ป้องกันการเดาข้อมูลผู้ใช้ |
| 6 | `currentUser_success` | ดึงข้อมูลผู้ใช้ปัจจุบันด้วย User ID ที่มีอยู่จริง | ส่งคืน `UserResponse` พร้อมข้อมูลโปรไฟล์ครบถ้วน |
| 7 | `currentUser_notFound_throwsException` | ดึงข้อมูลด้วย User ID ที่ไม่มีในระบบ | โยน `InvalidCredentialsException` |

---

### 👤 2.2 `UserProfileServiceUnitTest` (ระบบจัดการโปรไฟล์ผู้ใช้)
*ไฟล์ทดสอบ:* `com.mythix.tcg.user.application.UserProfileServiceUnitTest`  
*จำนวนเคส:* **6 Test Cases**

| ลำดับ | ชื่อ Test Case | วัตถุประสงค์และเงื่อนไขการทดสอบ | ผลลัพธ์ที่คาดหวัง |
|:---:|---|---|---|
| 1 | `getProfile_success` | ดึงข้อมูลโปรไฟล์ผู้ใช้ด้วย User ID ที่ถูกต้อง | ส่งคืน `ProfileResponse` พร้อมเบอร์โทร, Line, Facebook |
| 2 | `getProfile_userNotFound_throwsException` | ดึงข้อมูลโปรไฟล์ด้วย ID ที่ไม่มีจริง | โยน `InvalidCredentialsException` |
| 3 | `updateProfile_success` | อัปเดตข้อมูลทั่วไป (ชื่อ, เบอร์โทร, Line, Facebook, รูป Avatar) | ข้อมูลใน Entity อัปเดตถูกต้องตามที่ส่งมา |
| 4 | `updateProfile_changeEmail_success` | เปลี่ยนอีเมลใหม่ที่ไม่ซ้ำกับผู้อื่นในระบบ | อีเมลถูกแปลงเป็นตัวพิมพ์เล็ก (Normalized) และอัปเดตสำเร็จ |
| 5 | `updateProfile_duplicateEmail_throwsException` | เปลี่ยนไปใช้อีเมลที่มีคนอื่นใช้ไปแล้ว | โยน `DuplicateEmailException` ปฏิเสธการเปลี่ยนแปลง |
| 6 | `updateProfile_sameEmail_doesNotThrow` | ส่งอีเมลเดิม (หรือตัวพิมพ์ต่างกัน เช่น `ASH@MYTHIX.COM`) | ไม่โยน Error และไม่อัปเดตซ้ำซ้อน |

---

### 🛡️ 2.3 `SellerVerificationServiceUnitTest` (ระบบยืนยันตัวตนผู้ขาย)
*ไฟล์ทดสอบ:* `com.mythix.tcg.seller.application.SellerVerificationServiceUnitTest`  
*จำนวนเคส:* **3 Test Cases**

| ลำดับ | ชื่อ Test Case | วัตถุประสงค์และเงื่อนไขการทดสอบ | ผลลัพธ์ที่คาดหวัง |
|:---:|---|---|---|
| 1 | `verify_success` | ผู้ใช้ทั่วไป (Buyer) ยื่นเอกสารขอเป็นผู้ขาย | Role อัปเดตเป็น `SELLER`, สถานะเป็น `VERIFIED`, และสร้าง `SellerProfile` ร้านค้าใหม่ |
| 2 | `verify_alreadyVerified_throwsException` | ผู้ใช้ที่เป็นผู้ขายอยู่แล้วยื่นขอซ้ำ | โยน `SellerAlreadyVerifiedException` ป้องกันการเปิดร้านซ้ำซ้อน |
| 3 | `verify_userNotFound_throwsException` | ยื่นขอเป็นผู้ขายด้วย User ID ที่ไม่มีในระบบ | โยน `InvalidCredentialsException` |

---

### 🏪 2.4 `StoreProfileServiceUnitTest` (ระบบข้อมูลหน้าร้านของผู้ขาย)
*ไฟล์ทดสอบ:* `com.mythix.tcg.seller.application.StoreProfileServiceUnitTest`  
*จำนวนเคส:* **3 Test Cases**

| ลำดับ | ชื่อ Test Case | วัตถุประสงค์และเงื่อนไขการทดสอบ | ผลลัพธ์ที่คาดหวัง |
|:---:|---|---|---|
| 1 | `mine_success` | ผู้ขายดึงข้อมูลร้านค้าของตนเอง | ส่งคืน `StoreProfileResponse` พร้อมชื่อร้าน, รายละเอียด และช่องทางติดต่อ |
| 2 | `mine_notFound_throwsException` | ดึงข้อมูลร้านค้าของผู้ใช้ที่ยังไม่ได้ลงทะเบียนร้าน | โยน `InvalidCredentialsException` |
| 3 | `update_success` | แก้ไขข้อมูลชื่อร้านและคำอธิบายร้านค้า | ข้อมูลร้านค้าอัปเดตถูกต้อง |

---

### 📦 2.5 `SellerOrderServiceUnitTest` (ระบบจัดการออเดอร์และรายงานกำไรของผู้ขาย)
*ไฟล์ทดสอบ:* `com.mythix.tcg.order.application.SellerOrderServiceUnitTest`  
*จำนวนเคส:* **6 Test Cases**

| ลำดับ | ชื่อ Test Case | วัตถุประสงค์และเงื่อนไขการทดสอบ | ผลลัพธ์ที่คาดหวัง |
|:---:|---|---|---|
| 1 | `getSellerOrders_all_success` | ผู้ขายดึงรายการคำสั่งซื้อทั้งหมดแบบ Pagination | ส่งคืนรายการออเดอร์เฉพาะสินค้าของร้านค้านั้น |
| 2 | `getSellerOrders_filtered_success` | กรองรายการคำสั่งซื้อตามสถานะ (เช่น `PENDING`) | คิวรีเฉพาะออเดอร์ที่มีสถานะตรงกับเงื่อนไข |
| 3 | `getSellerOrderById_success` | ดูรายละเอียดคำสั่งซื้อรายออเดอร์ | ส่งคืนยอดเงิน, ข้อมูลผู้ซื้อ, และรายการการ์ดที่สั่ง |
| 4 | `getSellerOrderById_notFound_throwsException` | ค้นหาออเดอร์ที่ไม่ใช่ของร้านค้าตัวเอง | โยน `ResponseStatusException (404 NOT_FOUND)` ป้องกันการเข้าถึงข้ามร้าน |
| 5 | `updateOrderStatus_success` | อัปเดตสถานะออเดอร์ (เช่น เปลี่ยนเป็น `CONFIRMED`) | สถานะออเดอร์เปลี่ยนและบันทึกลงฐานข้อมูลสำเร็จ |
| 6 | `getProfitSummary_success` | คำนวณยอดขายรวม ต้นทุนรวม และกำไรสุทธิ | คำนวณ Revenue, Cost, และ Net Profit จากออเดอร์ที่ไม่ถูกยกเลิกได้อย่างแม่นยำ |

---

## 📊 3. สรุปสถิติผลการทดสอบทั้งหมด (Test Execution Summary)

ผลการสั่งรันชุดทดสอบทั้งหมดในโปรเจกต์ `tcg-store-backend` ผ่านคำสั่ง `./mvnw test`:

```text
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running com.mythix.tcg.auth.application.AuthServiceUnitTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0
[INFO] Running com.mythix.tcg.user.application.UserProfileServiceUnitTest
[INFO] Tests run: 6, Failures: 0, Errors: 0, Skipped: 0
[INFO] Running com.mythix.tcg.seller.application.SellerVerificationServiceUnitTest
[INFO] Tests run: 3, Failures: 0, Errors: 0, Skipped: 0
[INFO] Running com.mythix.tcg.seller.application.StoreProfileServiceUnitTest
[INFO] Tests run: 3, Failures: 0, Errors: 0, Skipped: 0
[INFO] Running com.mythix.tcg.order.application.SellerOrderServiceUnitTest
[INFO] Tests run: 6, Failures: 0, Errors: 0, Skipped: 0
[INFO] Running com.mythix.tcg.auth.api.AuthControllerIntegrationTests
[INFO] Tests run: 9, Failures: 0, Errors: 0, Skipped: 0
[INFO] Running com.mythix.tcg.seller.api.SellerProductControllerIntegrationTests
[INFO] Tests run: 17, Failures: 0, Errors: 0, Skipped: 0
[INFO] Running com.mythix.tcg.seller.application.SellerProductImageStorageTests
[INFO] Tests run: 6, Failures: 0, Errors: 0, Skipped: 0
[INFO] Running com.mythix.tcg.order.api.SellerOrderControllerIntegrationTests
[INFO] Tests run: 4, Failures: 0, Errors: 0, Skipped: 0
[INFO] Running com.mythix.tcg.TcgStoreBackendApplicationTests
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] Results:
[INFO] 
[INFO] Tests run: 62, Failures: 0, Errors: 0, Skipped: 0
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```

| หมวดหมู่การทดสอบ | จำนวนเคส | ผ่าน (Pass) | ไม่ผ่าน (Fail) | ข้อผิดพลาด (Error) |
|---|:---:|:---:|:---:|:---:|
| **Service Layer Unit Tests (ใหม่)** | **25** | **25** | **0** | **0** |
| Controller & Storage Integration Tests | 36 | 36 | 0 | 0 |
| Application Context Load Test | 1 | 1 | 0 | 0 |
| **รวมทั้งหมด (Total)** | **62** | **62** | **0** | **0** |

---

## 🚀 4. ข้อมูลการส่งมอบและ Pull Request

* **Repository:** `tcg-store-backend`
* **Branch ที่พัฒนา Unit Tests:** `Unit_test`
* **Target Branch:** `main`
* **Pull Request Link:** [PR #13: feat(test): Sprint 1 Core Services Unit Tests (SCRUM-36)](https://github.com/MUDST-2026-luntungsadungyong/tcg-store-backend/pull/13)
* **การทำงานบน CI Pipeline:** รันผ่านระบบ GitHub Actions เมื่อมีการเปิด Pull Request และได้ผลลัพธ์การทดสอบเป็นสีเขียว (✅) สมบูรณ์ตรงตามเกณฑ์ของ Sprint 1
