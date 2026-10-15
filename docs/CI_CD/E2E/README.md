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

| กลุ่มชุดทดสอบ | ไฟล์โค้ด Cypress (`.cy.ts`) | บทบาทผู้ใช้ (Actor) | จำนวนเทส & ไฮไลท์สำคัญ |
| :--- | :--- | :--- | :--- |
| **Auth & Security** | `cypress/e2e/auth/01_login_and_security.cy.ts` | Guest / Buyer | 6 tests - Login, Validations, Role Route Protection |
| **Auth & Security** | `cypress/e2e/auth/02_registration_and_signup.cy.ts` | Guest | 5 tests - Registration form, Validation, Email check |
| **Auth & Security** | `cypress/e2e/auth/03_forgot_and_reset_password.cy.ts` | Guest | 5 tests - Password recovery flow & token verification |
| **Auth & Security** | `cypress/e2e/auth/04_user_profile_and_contact.cy.ts` | Buyer | 4 tests - User profile, Contact channels & Shipping Address |
| **Admin Platform** | `cypress/e2e/admin/01_admin_access_and_authorization.cy.ts` | Admin / Unauthorized | 4 tests - Admin RBAC Guard, Unauthorized redirect |
| **Admin Platform** | `cypress/e2e/admin/02_admin_dashboard.cy.ts` | Admin | 4 tests - Overview KPIs, Metrics cards, Charts |
| **Admin Platform** | `cypress/e2e/admin/03_seller_application_queue.cy.ts` | Admin | 6 tests - Seller verification queue, Approve/Reject KYC |
| **Admin Platform** | `cypress/e2e/admin/04_seller_update_product.cy.ts` | Admin | 3 tests - Product moderation, Price & condition checks |
| **Admin Platform** | `cypress/e2e/admin/05_admin_users_management.cy.ts` | Admin | 3 tests - User management, Search, Role updates |
| **Admin Platform** | `cypress/e2e/admin/06_admin_audit_logs.cy.ts` | Admin | 1 test - System-wide audit log monitoring & filters |
| **Navigation & Discovery** | `cypress/e2e/01_navigation_and_discovery.cy.ts` | Guest / Buyer | 6 tests - Landing page, Search bar, Filters, Categories |
| **Cart & Checkout** | `cypress/e2e/03_cart_and_checkout_flow.cy.ts` | Buyer | 3 tests - Cart items, Quantity, Price calculation, Checkout |
| **Order Lifecycle** | `cypress/e2e/04_order_fastforward_lifecycle.cy.ts` | Buyer + Seller | 3 tests - Fast-Forward API status lifecycle & DB teardown |
| **Notifications** | `cypress/e2e/05_notifications_inbox.cy.ts` | Buyer / Guest | 14 tests - Inbox badges, Filters, Read/Unread, Guest redirect |
| **Seller Onboarding** | `cypress/e2e/05_seller_onboarding_journey.cy.ts` | Buyer / New Seller | 4 tests - Seller application form, Doc upload, Status |
| **Public Store** | `cypress/e2e/06_public_store_browsing.cy.ts` | Guest / Buyer | 3 tests - Seller storefront, Inventory browsing, Contact |
| **Feedback & Errors** | `cypress/e2e/07_feedback_and_errors.cy.ts` | Guest / Buyer | 5 tests - Toast notifications, Modals, 404, Stock errors |
| **Buyer Orders** | `cypress/e2e/08_buyer_order_history_and_direct_buy.cy.ts` | Buyer | 2 tests - Order history tab, Detail view, Buy Now flow |
| **Seller Inventory** | `cypress/e2e/09_seller_inventory_management.cy.ts` | Seller | 3 tests - Create listing, Edit price/stock, Toggle active |
| **Collection & Leaderboard**| `cypress/e2e/10_collection_and_leaderboard.cy.ts` | Buyer / Collector | 2 tests - Card collection tracking, Leaderboard rank |
| **รวมทั้งหมด** | **20 Specs** | **All Roles** | **86 / 86 ผ่าน 100% (Green)** |

---

## 🧩 การอ้างอิง Custom Commands กับ User Journeys

> ดูรายละเอียดเต็มได้ที่: [**แผนผังความสัมพันธ์ Custom Commands กับ Core User Journeys (`custom_commands_mapping.md`)**](./custom_commands_mapping.md)

| Custom Command | บทบาทการทำงาน | Journeys ที่อ้างอิงใช้งาน |
| :--- | :--- | :--- |
| **`cy.loginByMock(role)`** | สร้าง Session ล็อกอินจำลองผ่าน Local Storage | **Journey 3** (Cart), **Journey 4** (Order Flow) |
| **`cy.loginByUI(email, pwd)`** | กรอกฟอร์มเข้าสู่ระบบจริงบนหน้าเว็บและกด Submit | **Journey 2** (Flow 2.1 Login) |
| **`cy.registerNewUser(name, email, pwd)`** | กรอกฟอร์มสมัครสมาชิกใหม่ครบ 4 ช่องที่ `/register` | **Journey 2** (Flow 2.2 Sign-Up) |
| **`cy.requestPasswordReset(email)`** | กรอกอีเมลที่หน้า `/forgot-password` ขอรีเซ็ตรหัส | **Journey 2** (Flow 2.3 Reset Password) |
| **`cy.updateProfileName(name)`** | เข้าหน้า `/profile/edit` เปลี่ยนชื่อและบันทึก | **Journey 2** (Flow 2.4 Profile) |
| **`cy.updateProfileContact({...})`** | เข้าหน้า `/profile/edit/contact` อัปเดตช่องทางติดต่อ | **Journey 2** (Flow 2.4 Contact) |
| **`cy.sellerFastForwardOrder(id, status)`** | **API Fast-Forward:** ยิง PATCH อัปเดตสถานะส่งของโดยไม่สลับ UI | **Journey 4** (Order Flow) |
| **`cy.cleanTestOrder(id)`** | **Data Teardown:** ลบ Order ออกจาก Local PostgreSQL หลังเทสเสร็จ | **Journey 4** (Order Flow) |
| **`cleanupLocalDbOrder` (Node Task)** | ทาสก์คำสั่ง SQL Delete ใน Docker Container `tcg_postgres` | **Journey 4** (เรียกผ่าน `cy.cleanTestOrder`) |

---

## 💻 วิธีการสั่งรันการทดสอบ (Execution Commands)

ระบบรองรับการแยกการรันการทดสอบออกเป็นหลายระดับความลึก (Test Tiers) ตามความต้องการ:

### 📦 ตารางคำสั่งลัดใน `package.json`:

| คำสั่ง | รูปแบบการทดสอบ (Tier) | สิ่งที่ถูกรัน | ระยะเวลาโดยประมาณ |
| :--- | :--- | :--- | :--- |
| `npm run test:e2e:auth` | **Auth & Account Full Suite** | รันครบทั้ง 4 Flows ใน `cypress/e2e/auth/` (Login, Sign-Up, Reset Password, Profile) | ~30 - 45 วินาที |
| `npm run test:e2e:auth:open` | **Auth Interactive GUI** | เปิดหน้าต่าง Cypress เฉพาะโฟลเดอร์ Auth & Account | ตามการคลิก |
| `npm run test:e2e:full` หรือ `npm run test:e2e` | **Full Test** | รันครบทุก Journey (01 ถึง 04) | ~1.5 - 2 นาที |
| `npm run test:e2e:half` หรือ `npm run test:e2e:smoke` | **Half / Smoke Test** | รันเฉพาะแกนหลัก (01 Navigation + 02 Auth) | ~20 - 30 วินาที |
| `npm run test:e2e:order` | **Order & Custom Command Flow** | รันเฉพาะ Journey 04 ทดสอบ `sellerFastForwardOrder` และ `cleanTestOrder` | ~15 - 25 วินาที |
| `npm run test:e2e:open` | **Interactive GUI (All)** | เปิดหน้าต่าง Cypress ให้คลิกเลือกเทสทีละเคสสดๆ (สำหรับเดโมส่งงาน) | ตามการคลิก |

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

| `npm run test:e2e:admin` | **Admin Platform Suite** | รันเฉพาะ E2E ของ Admin Platform ทั้ง 3 Flow (US-11-1 ถึง US-11-3) | ~15 - 30 วินาที |
