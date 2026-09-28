# 🧭 Journey 1: การนำทางและค้นหาการ์ดสะสม (Navigation & Card Discovery)

> **อ้างอิง Spec File:** [`Code_devops/tcg-store-frontend/cypress/e2e/01_navigation_and_discovery.cy.ts`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/e2e/01_navigation_and_discovery.cy.ts)  
> **หมวดหมู่:** `E2E Testing / Core User Journey 1`  
> **กลุ่มผู้ใช้งาน (Actor):** Guest / ทั่วไป (Anonymous User)

---

## 📌 1. วัตถุประสงค์ (Objective)
ทดสอบประสบการณ์การเข้าใช้งานเว็บไซต์ในด่านแรก (First Impression) ของผู้ใช้ทั่วไปที่ไม่จำเป็นต้องล็อกอิน เพื่อให้มั่นใจว่า:
1. หน้าแรก (Landing Page) โหลดองค์ประกอบสำคัญครบถ้วน ไม่แตก
2. เมนูนำทาง (Navbar / Header) สามารถคลิกพาไปยังหน้ารวมการ์ดได้ถูกต้อง
3. ช่องค้นหาการ์ด (Global Search Bar) ตอบสนองต่อการพิมพ์คำค้นหาและนำทางไปยังหน้ารวมการ์ดพร้อมผลลัพธ์

---

## 🧪 2. รายละเอียด Test Scenarios

### 🔹 TC-E2E-NAV-01: การโหลดหน้าแรกและองค์ประกอบหลัก (Landing Page Rendering)
* **Pre-condition:** ผู้ใช้ยังไม่ได้เข้าสู่ระบบ
* **Action:** `cy.visit('/')`
* **Assertions:**
  * หน้าจอแสดงผลส่วน Header และ Navigation Links
  * ตัวหนังสือพาดหัวหลักและปุ่ม Call-to-Action (CTA) ปรากฏสมบูรณ์
  * ไม่เกิด Error จอขาว (Uncaught Exception)

### 🔹 TC-E2E-NAV-02: การนำทางไปยังหน้ารวมการ์ด (Discovery Navigation)
* **Pre-condition:** อยู่ที่หน้าแรก (`/`)
* **Action:** คลิกที่ลิงก์หรือปุ่มนำทางไปยังตลาดการ์ด (เช่น ลิงก์ไปยัง `/cards` หรือ "Browse Cards")
* **Assertions:**
  * URL เปลี่ยนเป็นพาธที่มีคำว่า `/cards`
  * โครงสร้าง Grid แสดงรายการการ์ดถูกเรนเดอร์ขึ้นมาบนหน้าจอ

### 🔹 TC-E2E-NAV-03: การค้นหาการ์ดด้วยคีย์เวิร์ด (Search Bar Interaction)
* **Pre-condition:** หน้าแรกหรือหน้ารวมการ์ด
* **Action:**
  1. ค้นหา `input[placeholder*="Search"]` หรือช่องค้นหาใน Navbar
  2. พิมพ์คำค้นหา เช่น `Pikachu` พร้อมกด Enter (`{enter}`)
* **Assertions:**
  * URL เปลี่ยนเป็น `/cards?q=Pikachu` หรือหน้ารวมผลลัพธ์
  * หน้าจอแสดงผลการ์ดที่ตรงกับคำค้นหา

---

## 📋 3. ข้อมูลทางเทคนิค (Technical Details & Selectors)

| องค์ประกอบ | Selector / คำสั่ง Cypress | ค่าที่คาดหวัง |
| :--- | :--- | :--- |
| **Navbar Root** | `cy.get('header, nav')` | `be.visible` |
| **Search Input** | `cy.get('input[type="search"], input[placeholder*="Search"]')` | รับข้อความและกด Enter |
| **Browse Link** | `cy.contains('Browse Cards')` | นำทางไป `/cards` |

---

## 🔮 4. แผนการต่อขยายในอนาคต (Future Expansion)
* [ ] การทดสอบ Filter ตามประเภทการ์ด (Pokémon, Magic, Yu-Gi-Oh!)
* [ ] การทดสอบการเรียงลำดับราคา (Sort: Low to High / High to Low)
* [ ] การทดสอบเปิดดูโมเดลการ์ดแบบ 3 มิติ (3D Card Viewer Component)
