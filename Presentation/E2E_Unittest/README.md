# ชุดข้อมูลการนำเสนอ: E2E & Unit Testing (ISP Strategy)
**โฟลเดอร์:** `tcg-store-document/Presentation/E2E_Unittest`

โฟลเดอร์นี้รวบรวมเอกสารและชุดข้อมูลสรุปทั้งหมดเกี่ยวกับการทดสอบซอฟต์แวร์ของโปรเจกต์ **Mythix TCG Store** โดยเน้นการอธิบายตามหลักการ **Input Space Partitioning (ISP)** ทั้งในระดับ Unit Test และ E2E Test สำหรับใช้ประกอบการนำเสนอ (Presentation) และตอบข้อซักถามในการตรวจประเมินโปรเจกต์

---

## 📂 สารบัญเอกสารในโฟลเดอร์นี้

1. 📄 **[`01_ISP_Testing_Strategy_Report.md`](file:///c:/Devops/tcg-store-document/Presentation/E2E_Unittest/01_ISP_Testing_Strategy_Report.md)**
   * รายงานฉบับสมบูรณ์ (Comprehensive Report)
   * อธิบายทฤษฎี Input Space Partitioning (ISP) และ Test Pyramid
   * ตาราง Characteristic Modeling & Partitioning ครบทุกโดเมน (Auth, Cart, Stock, Roles, Inventory, Orders)
   * หลักฐานผลลัพธ์การรัน Test (Verification Metrics)

2. 🖥️ **[`02_ISP_Presentation_Slide_Deck.md`](file:///c:/Devops/tcg-store-document/Presentation/E2E_Unittest/02_ISP_Presentation_Slide_Deck.md)**
   * เอกสารสไลด์นำเสนอ (Slide-by-Slide Deck)
   * มีข้อความสรุปกระชับสำหรับขึ้นจอพรีเซ็นต์ (Bullet points & Tables)
   * **Speaker Talking Points** สำหรับผู้พูดในแต่ละสไลด์
   * **Q&A Cheatsheet** รวมคำถามที่อาจารย์/กรรมการมักจะถาม พร้อมแนวทางการตอบอย่างเป็นมืออาชีพ

---

## 📊 ตัวเลขสรุปสำหรับการนำเสนอ (Quick Reference Numbers)

* **รวม Test ทั้งระบบ (Grand Total):** **518+ tests** (107 files)
* **Frontend Unit & Component (Vitest):** **234 tests** (51 files) — ผ่าน 100%
* **Frontend E2E Journeys (Cypress):** **99 tests** (21 files) — ผ่าน 100%
* **Backend Unit & Controller (JUnit 5):** **185+ tests** (35 files) — ผ่าน 100%
* **API & Controller Coverage:** **100%** (20/20 Backend Controllers และทุก Frontend API Clients)
