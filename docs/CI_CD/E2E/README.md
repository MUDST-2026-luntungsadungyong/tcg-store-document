# 🌲 Cypress End-to-End (E2E) Test Suite Catalog

> **โปรเจกต์:** Mythix TCG Marketplace Platform  
> **หมวดหมู่:** `CI_CD / E2E`  
> **เครื่องมือหลัก:** Cypress v16.1.0  
> **สถานะ:** 🟢 พร้อมใช้งานและรองรับการขยายผล (Scalable Architecture)

---

## 📌 สารบัญชุดทดสอบ Core User Journeys (Directory Index)

การทดสอบระดับ E2E ถูกจัดแบ่งออกเป็นหมวดหมู่ตามเส้นทางการใช้งานของผู้ใช้ (User Journeys) เพื่อให้ง่ายต่อการบำรุงรักษาและการเพิ่ม Use Case ใหม่ๆ ในอนาคต:

```text
docs/CI_CD/E2E/
├── README.md                                    # 📖 หน้าสารบัญหลักนี้
├── cypress_e2e_testing_guide.md                 # ⚙️ คู่มือสถาปัตยกรรม & การตั้งค่า Cypress ทั่วไป
├── custom_commands_mapping.md                   # 🧩 แผนผัง Custom Commands กับ Journeys
│
├── 01_Navigation_and_Discovery/                 # 🧭 Journey 1
│   └── README.md                                # รายละเอียด: ค้นหาการ์ด, หน้าแรก, Navbar
│
├── 02_Authentication_and_Security/              # 🔐 Journey 2
│   └── README.md                                # รายละเอียด: ระบบล็อกอิน, Route Protection (/admin, /workspace)
│
├── 03_Cart_and_Inventory/                       # 🛒 Journey 3
│   └── README.md                                # รายละเอียด: ตะกร้าสินค้า, สต็อกการ์ด, คำนวณราคา
│
└── 04_Order_Fulfillment_Lifecycle/              # 📦 Journey 4
    └── README.md                                # รายละเอียด: สั่งซื้อ ➔ ส่งของ (API Fast-Forward) ➔ Teardown
```

---

## 📊 ตารางสรุปภาพรวมชุดทดสอบ (Test Suites Overview)

| โฟลเดอร์เอกสาร | ไฟล์โค้ด Cypress (`.cy.ts`) | บทบาทผู้ใช้ (Actor) | จุดเด่นสำคัญ (Key Highlights) |
| :--- | :--- | :--- | :--- |
| [**01_Navigation_and_Discovery**](./01_Navigation_and_Discovery/README.md) | `01_navigation_and_discovery.cy.ts` | Guest | ตรวจสอบ Landing Page, เมนูหลัก, และ Search Bar Interaction |
| [**02_Authentication_and_Security**](./02_Authentication_and_Security/README.md) | `02_auth_and_protected_routes.cy.ts` | Guest / Attacker | ตรวจสอบฟอร์ม Login, Empty Validation, และ Route Guard เตะกลับ |
| [**03_Cart_and_Inventory**](./03_Cart_and_Inventory/README.md) | `03_cart_and_checkout_flow.cy.ts` | Buyer (`buyer@mythix.com`) | ตรวจสอบสถานะตะกร้าว่าง, การแสดงสินค้า, และยอดรวมก่อนจอง |
| [**04_Order_Fulfillment_Lifecycle**](./04_Order_Fulfillment_Lifecycle/README.md) | `04_order_fastforward_lifecycle.cy.ts` | Buyer + Seller | **API Fast-Forward** ไม่ต้องสลับ Account + **Local DB Teardown** |

---

## 🧩 การอ้างอิง Custom Commands กับ User Journeys

> ดูรายละเอียดเต็มได้ที่: [**แผนผังความสัมพันธ์ Custom Commands กับ Core User Journeys (`custom_commands_mapping.md`)**](./custom_commands_mapping.md)

| Custom Command | บทบาทการทำงาน | Journeys ที่อ้างอิงใช้งาน |
| :--- | :--- | :--- |
| **`cy.loginByMock(role)`** | สร้าง Session ล็อกอินจำลองผ่าน Local Storage | **Journey 3** (Cart), **Journey 4** (Order Flow) |
| **`cy.sellerFastForwardOrder(id, status)`** | **API Fast-Forward:** ยิง PATCH อัปเดตสถานะส่งของโดยไม่สลับ UI | **Journey 4** (Order Flow) |
| **`cy.cleanTestOrder(id)`** | **Data Teardown:** ลบ Order ออกจาก Local PostgreSQL หลังเทสเสร็จ | **Journey 4** (Order Flow) |
| **`cleanupLocalDbOrder` (Node Task)** | ทาสก์คำสั่ง SQL Delete ใน Docker Container `tcg_postgres` | **Journey 4** (เรียกผ่าน `cy.cleanTestOrder`) |

---

## 💻 วิธีการสั่งรันการทดสอบ (Execution Commands)

ระบบรองรับการแยกการรันการทดสอบออกเป็นหลายระดับความลึก (Test Tiers) ตามความต้องการ:

### 📦 ตารางคำสั่งลัดใน `package.json`:

| คำสั่ง | รูปแบบการทดสอบ (Tier) | สิ่งที่ถูกรัน | ระยะเวลาโดยประมาณ |
| :--- | :--- | :--- | :--- |
| `npm run test:e2e:full` หรือ `npm run test:e2e` | **Full Test** | รันครบทุก Journey (01 ถึง 04) | ~1.5 - 2 นาที |
| `npm run test:e2e:half` หรือ `npm run test:e2e:smoke` | **Half / Smoke Test** | รันเฉพาะแกนหลัก (01 Navigation + 02 Auth) | ~20 - 30 วินาที |
| `npm run test:e2e:order` | **Order & Custom Command Flow** | รันเฉพาะ Journey 04 ทดสอบ `sellerFastForwardOrder` และ `cleanTestOrder` | ~15 - 25 วินาที |
| `npm run test:e2e:open` | **Interactive GUI** | เปิดหน้าต่าง Cypress ให้คลิกเลือกเทสทีละเคสสดๆ (สำหรับเดโมส่งงาน) | ตามการคลิก |

---

### 🔍 ตัวอย่างการสั่งรันเฉพาะไฟล์หรือส่ง Parameter ผ่าน CLI:
```bash
# 1. รันเฉพาะ Journey 4 (คำสั่ง Fast-Forward & Teardown)
npx cypress run --spec "cypress/e2e/04_order_fastforward_lifecycle.cy.ts"

# 2. รันเฉพาะ Journey 1 และ 3
npx cypress run --spec "cypress/e2e/01_*,cypress/e2e/03_*"
```

---

## 🔮 แผนการเพิ่ม Use Case ใน Sprint ถัดไป (Roadmap)

1. **Sprint 4 — Notification & Tracking Number:**
   * ผู้ขายกรอกเลขพัสดุ (Tracking Number) พร้อมลิงก์ขนส่ง (Flash / Kerry / ไปรษณีย์ไทย)
   * ผู้ซื้อได้รับ Email แจ้งเตือนสถานะผ่าน Mailpit
2. **Sprint 4 — Seller Tier & Commission Fee:**
   * ตรวจสอบการหักค่าธรรมเนียมแพลตฟอร์ม 10% เมื่อผู้ขายแต่ละระดับ Tier ทำการขายสำเร็จ
3. **Card Condition & Grading:**
   * การเลือกการ์ดตามสภาพ (Mint / Near Mint / Played) และการ์ดแท้ที่ผ่านการเกรด (PSA / BGS)
