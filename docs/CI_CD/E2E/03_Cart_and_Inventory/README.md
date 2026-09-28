# 🛒 Journey 3: ตะกร้าสินค้าและการตรวจสอบสต็อก (Cart & Inventory Management)

> **อ้างอิง Spec File:** [`Code_devops/tcg-store-frontend/cypress/e2e/03_cart_and_checkout_flow.cy.ts`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/e2e/03_cart_and_checkout_flow.cy.ts)  
> **หมวดหมู่:** `E2E Testing / Core User Journey 3`  
> **กลุ่มผู้ใช้งาน (Actor):** ผู้ซื้อที่เข้าสู่ระบบแล้ว (Authenticated Buyer)

---

## 📌 1. วัตถุประสงค์ (Objective)
ทดสอบระบบตะกร้าสินค้า (Reservation Cart) ซึ่งเป็นหัวใจสำคัญก่อนเริ่มขั้นตอนสั่งซื้อ:
1. ผู้ซื้อที่มี Session ถูกต้องสามารถเข้าหน้าตะกร้าสินค้า (`/cart`) ได้อย่างราบรื่น
2. ตรวจสอบสถานะเมื่อไม่มีสินค้าในตะกร้า (Empty Cart State) แสดงผลชัดเจนพร้อมปุ่มเชิญชวนไปเลือกซื้อการ์ด
3. ตรวจสอบการแสดงรายการการ์ด, จำนวน (Quantity), ราคาต่อหน่วย, และราคารวมที่คำนวณถูกต้อง

---

## 🧪 2. รายละเอียด Test Scenarios

### 🔹 TC-E2E-CART-01: การเข้าถึงหน้าตะกร้าของผู้ซื้อ (Cart Access)
* **Pre-condition:** จำลองผู้ซื้อเข้าสู่ระบบผ่าน `cy.loginByMock('CUSTOMER')`
* **Action:** `cy.visit('/cart')`
* **Assertions:**
  * URL อยู่ที่ `/cart`
  * หน้าจอไม่เด้งหลุดไปหน้า `/login`

### 🔹 TC-E2E-CART-02: การแสดงผลกรณีตะกร้าว่างเปล่า (Empty State)
* **Pre-condition:** ตะกร้าสินค้าของผู้ซื้อไม่มีรายการใดๆ (`items: []`)
* **Action:** เข้าหน้า `/cart`
* **Assertions:**
  * หน้าจอแสดงข้อความ *"Your reservation cart is empty"*
  * แสดงปุ่ม *"Browse cards"* ที่มีลิงก์พาไปยัง `/cards`

### 🔹 TC-E2E-CART-03: การแสดงรายการสินค้าและการคำนวณยอด (Cart Summary & Calculation)
* **Pre-condition:** ใส่การ์ดลงในตะกร้า (เช่น การ์ด Mew 1 ใบ ราคา 3,500 บาท)
* **Action:** เข้าหน้า `/cart`
* **Assertions:**
  * แสดงรูปภาพ, ชื่อการ์ด, และชื่อผู้ขาย (Seller ID)
  * แสดงจำนวนสินค้าและปุ่มบวก/ลบจำนวน
  * แสดงกล่องสรุปยอด "Reservation summary" คำนวณราคาถูกต้อง
  * ปุ่ม *"Review reservation"* ปรากฏพร้อมคลิกไปสู่หน้า Checkout

---

## 📋 3. ข้อมูลทางเทคนิค (Technical Details & Stores)

| Store / State | บทบาทหน้าที่ |
| :--- | :--- |
| **`useReservationCartStore`** | Zustand Store จัดการ `items`, `updateQuantity()`, `removeItem()`, `totalAmount` |
| **Session Key** | `tcg_cart_storage` หรือคุยกับ Backend Endpoint `GET /api/cart` |
| **Custom Command** | `cy.loginByMock('CUSTOMER')` เซ็ต Session ให้ระบบมองเห็นเป็น Buyer อัตโนมัติ |

---

## 🔮 4. แผนการต่อขยายในอนาคต (Future Expansion)
* [ ] การทดสอบกดปุ่มบวก (+) / ลบ (-) เพื่อเปลี่ยนจำนวนสินค้า แล้วยอดรวมเปลี่ยนแบบ Real-time
* [ ] การทดสอบลบสินค้าออกจากตะกร้า (Trash icon)
* [ ] การทดสอบการแยกกลุ่มสินค้าตามผู้ขาย (Multi-Seller Grouping ในตะกร้าเดียว)
* [ ] การทดสอบกรณีสินค้าในสต็อกหมด (Out of Stock / Insufficient Inventory Banner)
