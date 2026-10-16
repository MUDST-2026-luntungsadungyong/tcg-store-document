# สไลด์นำเสนอ: กลยุทธ์การทดสอบตามหลัก ISP (Input Space Partitioning)
**หัวข้อ:** การประกันคุณภาพซอฟต์แวร์ด้วย E2E Testing และ Unit Testing ตามหลัก ISP ใน Mythix TCG Store  
**เป้าหมาย:** สไลด์สรุปเนื้อหาสำหรับใช้เปิดพรีเซ็นต์ พร้อม Talking Points และแนวทางการตอบคำถามกรรมการ

---

## 🖥️ Slide 1: หน้าปก (Title Slide)
* **หัวข้อ:** Test Automation Strategy & Input Space Partitioning (ISP) Implementation
* **โปรเจกต์:** Mythix TCG Store (Full-Stack Marketplace Platform)
* **สโลแกน:** "High Coverage, Zero Flakiness, and Optimized Test Efficiency"
* **Speaker Talking Point:**
  > "สวัสดีครับ วันนี้พวกเราจะมานำเสนอกลยุทธ์การประกันคุณภาพซอฟต์แวร์ของโปรเจกต์ Mythix TCG Store โดยเรามุ่งเน้นการประยุกต์ใช้หลักการ **Input Space Partitioning (ISP)** ทั้งในระดับ Unit Test และ End-to-End Test เพื่อให้ระบบของเรามีคุณภาพสูงสุดและครอบคลุมทุกจุดเสี่ยงทางธุรกิจครับ"

---

## 🖥️ Slide 2: ภาพรวมและผลลัพธ์เชิงตัวเลข (Overall Test Metrics)
* **ตัวเลขผลลัพธ์การทดสอบทั้งระบบ:**
  * 🟢 **รวมการทดสอบทั้งหมด (Grand Total):** **518+ Test Cases** (107 Test Files)
  * 🟢 **Frontend Unit Tests (Vitest):** **234 Tests** (51 Files) — ผ่าน 100%
  * 🟢 **Frontend E2E Tests (Cypress):** **99 Tests** (21 Files) — ผ่าน 100%
  * 🟢 **Backend Unit & Integration Tests (JUnit 5):** **185+ Tests** (35 Files) — ผ่าน 100%
  * 🟢 **API Coverage:** **100%** (ครอบคลุมทั้ง 20 Backend Controllers และทุก Frontend API Clients)
* **Speaker Talking Point:**
  > "จากภาพรวม เราสร้างชุดทดสอบครอบคลุมทุกระดับตามแนวทาง Test Pyramid โดยมี Unit Test กว่า 419 เคสเพื่อเช็คตรรกะในระดับฟังก์ชันอย่างรวดเร็ว และมี Cypress E2E Test อีก 99 เคสเพื่อจำลองพฤติกรรมผู้ใช้จริงตั้งแต่ต้นจนจบ โดยผลการรันล่าสุดผ่านฉลุย 100% ครับ"

---

## 🖥️ Slide 3: ทำไมต้องเป็น "ISP" (Input Space Partitioning)?
* **ความท้าทาย (The Challenge):**
  * ถ้าเราทดสอบทุกข้อมูลที่ผู้ใช้อาจกรอก (All Combinations) ใน E2E จะทำให้เกิด **Combinatorial Explosion** การรัน E2E อาจใช้เวลาหลายชั่วโมงและเกิดอาการ Flaky Test ได้ง่าย
* **ทางแก้ด้วย ISP (The Solution):**
  * **Equivalence Partitioning:** แบ่ง Input Domain ออกเป็นกลุ่มข้อมูลที่ให้ผลลัพธ์เทียบเท่ากัน (Valid vs Invalid)
  * **Boundary Value Analysis (BVA):** มุ่งเน้นทดสอบจุดตัดหรือขอบเขตวิกฤต เช่น สต็อกเป็น 0, ซื้อเกินสต็อก
  * **Base Choice Coverage (BCC):** กำหนดแกนหลักค่าปกติ (Base Choice) แล้วทดสอบเปลี่ยนทีละ Parameter ช่วยลดจำนวน Test Case ลงได้มหาศาลโดยไม่เสียความสามารถในการดักจับข้อผิดพลาด
* **Speaker Talking Point:**
  > "ทำไมเราถึงใช้ ISP? เพราะถ้าเราเขียน Test แบบสุ่มหรือเขียนทุก Combination ใน Cypress จะทำให้รันช้ามาก แต่หลัก ISP สอนให้เราวิเคราะห์ Characteristics และเลือกตัวแทนจากแต่ละ Partition ทำให้เราใช้เพียง 99 เคสใน Cypress แต่ครอบคลุมทุก Business Flow ได้อย่างสมบูรณ์แบบครับ"

---

## 🖥️ Slide 4: การจำแนก Characteristic ในระบบ (Characteristic Matrix)

| โดเมน (Domain) | คุณลักษณะที่วิเคราะห์ (Characteristic) | Partitions / Blocks ที่แบ่ง | พฤติกรรมที่คาดหวัง |
| :--- | :--- | :--- | :--- |
| **Authentication** | Password Complexity | สั้นกว่า 8 ตัว / สมบูรณ์ตามเกณฑ์ | แสดง Validation Checklist แจ้งเตือน |
| **Authentication** | Password Confirmation | ไม่ตรงกัน (Mismatch) / ตรงกัน | แจ้งเตือน "Passwords do not match" |
| **Cart & Stock** | Quantity vs Stock Boundary | ภายในสต็อก ($Q \le S$) / เกินสต็อก ($Q > S$) | ปุ่ม Increase ถูก `disabled` ทันที |
| **Cart & Stock** | Cart Population State | ว่างเปล่า ($Q = 0$) / มีสินค้า ($Q \ge 1$) | แสดง Empty State และซ่อนปุ่ม Checkout |
| **User Roles** | Persona Authorization | Guest / Buyer / Seller / Admin | Guest ถูก Redirect หรือเด้ง Login Modal |
| **Inventory** | Price & Cost Values | ปกติ ($>0$) / ติดลบหรือเป็นศูนย์ ($\le 0$) | โยน `ListingError` บล็อกการบันทึก |
| **Inventory** | Stock Safe Integer | จำนวนเต็มบวก / ทศนิยมหรือติดลบ | ปฏิเสธค่าที่ไม่ใช่ Safe Integer |

* **Speaker Talking Point:**
  > "ในสไลด์นี้คือตัวอย่าง Characteristic Matrix ที่เราสร้างขึ้น เช่น ในระบบตะกร้าสินค้า เราแบ่งขอบเขตของจำนวนสินค้าเทียบกับสต็อกเป็น 2 กลุ่มชัดเจน คือกลุ่มที่ซื้อได้ปกติ กับกลุ่มที่พยายามซื้อเกินสต็อก เพื่อให้มั่นใจว่าจะไม่มีข้อผิดพลาด Overselling เกิดขึ้นในระบบครับ"

---

## 🖥️ Slide 5: การประยุกต์ใช้ใน Cypress E2E (99 Test Cases)
* **จุดเด่นทางสถาปัตยกรรม (Architecture Highlights):**
  1. **Zero Inter-test Dependency:** แต่ละ Test Case รันแยกเป็นอิสระต่อกัน มีการ Clean Storage ใน `beforeEach` ทำให้ไม่มีปัญหาลำดับ Test ตีกัน
  2. **Mock Authentication Optimization:** ใช้ Custom Command `cy.loginByMock(role)` ข้ามหน้า UI Login ในเคสที่ไม่จำเป็น ช่วยประหยัดเวลาการรัน E2E ลงได้กว่า **60%**
  3. **Contextual Login Trigger:** เมื่อ Guest พยายามกดสะสมการ์ด ระบบจะไม่เปลี่ยนหน้าหนี แต่เปิด Contextual Login Modal ขึ้นมาแทน
* **Speaker Talking Point:**
  > "ในฝั่ง Cypress เราแบ่งเป็น 21 Spec Files ครอบคลุมทั้งฝั่ง Buyer, Seller, และ Admin นอกจากนี้เรายังออกแบบให้แต่ละ Test เป็นอิสระจากกันอย่างสมบูรณ์ ไม่มีการส่งต่อข้อมูลข้ามเคส ทำให้ไม่มีปัญหา Flaky Test เลยครับ"

---

## 🖥️ Slide 6: การประยุกต์ใช้ใน Unit & Integration Tests (419+ Tests)
* **Frontend Vitest (234 Tests):**
  * ครอบคลุม API Client ทุกไฟล์ (`cartApi`, `listingsApi`, `usersApi`, `catalog`, `orders`)
  * ตรวจสอบ Data Adapter และ Envelope Unwrapping (รองรับทั้ง Raw Array, Spring Page, และ API Envelopes)
  * ตรวจสอบ Edge Cases: ข้อมูลที่เป็น `null`, `undefined`, ค่าว่าง `""`, และ URL Injection
* **Backend JUnit 5 (185+ Tests):**
  * ครอบคลุม Controller ครบทั้ง 20 ตัวในระบบ
  * ตรวจสอบ Exception Handling เช่น `DuplicateEmailException`, `OrderNotFoundException`, `SellerApplicationStateException`
* **Speaker Talking Point:**
  > "สำหรับงานตรวจสอบอย่างละเอียด เช่น ข้อมูลเป็น null, ช่องว่าง, ตัวเลขติดลบ เราผลักภาระเหล่านี้มาอยู่ที่ Unit Test ทั้งฝั่ง Vitest และ JUnit ทำให้เราตรวจจับข้อผิดพลาดในระดับ Function ได้ภายในเวลาเสี้ยววินาทีครับ"

---

## 🖥️ Slide 7: สรุปคุณค่าที่ระบบได้รับ (Engineering Impact)
* 🚀 **รวดเร็ว (Fast Execution):** รัน Unit Test ทั้ง 234 ตัวจบภายในเวลาไม่ถึง 90 วินาที
* 🛡️ **มั่นใจ (Reliability):** ครอบคลุม 100% ของ Endpoints ไม่มีการปล่อยผ่าน API ใดโดยไม่มี Test
* 📈 **บำรุงรักษาง่าย (Maintainability):** โค้ดทดสอบถูกจัดโครงสร้างตาม Feature Domain และหลัก Interface Segregation (ISP ในเชิง SOLID) ทำให้ขยายระบบได้ง่าย

---

## 💡 แนวทางการตอบคำถามกรรมการ / อาจารย์ (Q&A Cheatsheet)

### Q1: ทำไมใน Cypress E2E ถึงไม่ทดสอบกรอกข้อมูลผิดทุกรูปแบบ (Negative Cases)?
> **แนวทางการตอบ:**  
> "เนื่องจากตามหลักการ **Test Pyramid** และ **Input Space Partitioning**, E2E Test มี Execution Cost สูง การทดสอบทุก Combination ใน E2E จะทำให้ CI/CD รันช้ามากและไม่คุ้มค่า เราจึงเลือกใช้เกณฑ์ **Base Choice Coverage (BCC)** ใน Cypress เพื่อตรวจสอบเฉพาะ Critical Flow และ Boundary สำคัญ (เช่น สต็อกเต็ม, การโดน Redirect) แล้วส่งต่อหน้าที่การทดสอบ Input Validation ยิบย่อยไปให้ **Vitest Unit Tests** ซึ่งทำงานได้เร็วกว่าหลายร้อยเท่าครับ"

### Q2: หลักการ ISP นำมาช่วยลดเวลาในการทดสอบได้อย่างไร?
> **แนวทางการตอบ:**  
> "ISP ช่วยให้เราไม่ต้องสุ่มเขียน Test เคสซ้ำๆ ที่อยู่ใน Equivalence Partition เดียวกัน เช่น ถ้าช่องป้อนรหัสผ่านรองรับ 8-72 ตัวอักษร เราไม่จำเป็นต้องทดสอบความยาว 9, 10, 11 ตัวซ้ำๆ แต่ทดสอบแค่ค่าตัวแทน 1 ค่า และทดสอบจุดตัด Boundary ที่ 7 ตัว (ผิด) กับ 8 ตัว (ถูก) ทำให้เราได้จำนวน Test Cases ที่กระชับที่สุดแต่คงความครอบคลุมบั๊กได้เท่าเดิมครับ"

### Q3: ปัจจุบันระบบมี API Coverage อยู่ที่เท่าไหร่?
> **แนวทางการตอบ:**  
> "ปัจจุบันระบบของเรามี **API Coverage ครบ 100% ทั้ง 20 Controllers ในฝั่ง Backend และทุก API Client ในฝั่ง Frontend** ครับ โดยมีการเขียนทั้ง Controller Test และ Unit Test ดักจับทั้งกรณีสำเร็จ (200 OK) และกรณีเกิดข้อผิดพลาด (400, 404, Conflict) ครบทุกตัวครับ"
