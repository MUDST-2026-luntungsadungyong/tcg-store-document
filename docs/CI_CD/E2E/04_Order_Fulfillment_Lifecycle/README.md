# 📦 Journey 4: วงจรคำสั่งซื้อเต็มรูปแบบ (Buyer Purchase ➔ Seller Fast-Forward ➔ Teardown)

> **อ้างอิง Spec File:** [`Code_devops/tcg-store-frontend/cypress/e2e/04_order_fastforward_lifecycle.cy.ts`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/e2e/04_order_fastforward_lifecycle.cy.ts)  
> **หมวดหมู่:** `E2E Testing / Core User Journey 4`  
> **กลุ่มผู้ใช้งาน (Actors):** ผู้ซื้อ (`buyer@mythix.com`) และ ผู้ขาย (`seller@mythix.com`)

---

## 📌 1. วัตถุประสงค์ (Objective)
ทดสอบกระบวนการทางธุรกิจหลักที่ซับซ้อนที่สุดของแพลตฟอร์ม (End-to-End Business Flow) ครอบคลุมผู้ใช้ 2 บทบาท:
1. ผู้ซื้อตรวจสอบรายการในตะกร้า ➔ ยืนยันการสั่งซื้อ (Checkout)
2. ระบบออกหมายเลขคำสั่งซื้อ (`orderId`) และแสดงหน้า Confirmation
3. **เทคนิค API Fast-Forward:** ผู้ขายยืนยันการจัดส่งผ่านการยิง API เบื้องหลัง โดยไม่ต้องสลับ Account บน UI
4. ผู้ซื้อตรวจสอบสถานะคำสั่งซื้อในหน้า `/orders` ว่าเปลี่ยนเป็น **"Sent"** สำเร็จ
5. **Data Teardown:** ล้างข้อมูลคำสั่งซื้อทดสอบออกจากฐานข้อมูล Local ทันทีเมื่อเทสเสร็จสิ้น

---

## 🔄 2. แผนภาพแสดงการทำงาน (End-to-End Execution Sequence)

```mermaid
sequenceDiagram
    autonumber
    actor Buyer as 🛒 ผู้ซื้อ (UI Test Runner)
    participant FE as 🌐 React Frontend (:5173)
    participant BE as ☕ Spring Boot Backend (:8080)
    participant DB as 🐘 Local Postgres (tcg_postgres)
    actor SellerAPI as ⚡ Cypress Seller API Hook

    Note over Buyer,FE: ขั้นตอนที่ 1: ผู้ซื้อทำรายการผ่านหน้าเว็บจริง
    Buyer->>FE: เปิดหน้า /cart กด "Review reservation"
    FE->>Buyer: นำทางไปหน้า /checkout
    Buyer->>FE: คลิกปุ่ม "Confirm reservation"
    FE->>BE: POST /api/orders (สร้าง Order สถานะ PENDING)
    BE->>DB: บันทึกข้อมูลลงตาราง orders & order_items
    BE-->>FE: ส่ง Response กลับ (Order ID)
    FE->>Buyer: นำทางไป /checkout/confirmation?orderIds={orderId}

    Note over SellerAPI,DB: ขั้นตอนที่ 2: จำลองผู้ขายจัดส่งของผ่าน API (50ms)
    SellerAPI->>BE: POST /api/auth/login (ขอ Token ผู้ขาย)
    SellerAPI->>BE: PATCH /api/seller/orders/{orderId}/status { status: "FULFILLED" }
    BE->>DB: อัปเดตสถานะในตาราง orders เป็น FULFILLED

    Note over Buyer,FE: ขั้นตอนที่ 3: ผู้ซื้อตรวจสอบผลการจัดส่ง
    Buyer->>FE: คลิก "View my orders" เข้าสู่ /orders
    FE->>BE: GET /api/orders/me
    BE-->>FE: ส่งข้อมูลคำสั่งซื้อที่มีสถานะ FULFILLED
    FE->>Buyer: แสดง Badge สถานะ "Sent" (สีเขียวพร้อมไอคอน Truck)

    Note over SellerAPI,DB: ขั้นตอนที่ 4: ล้างข้อมูลทดสอบ (Teardown Hook)
    SellerAPI->>DB: DELETE FROM order_items ...; DELETE FROM orders ...;
```

---

## 🧪 3. รายละเอียด Test Scenarios

### 🔹 TC-E2E-ORD-01: ผู้ซื้อยืนยันการสั่งซื้อ (Buyer Checkout Submission)
* **Pre-condition:** ผู้ซื้อล็อกอินด้วย `buyer@mythix.com` และมีสินค้าในตะกร้า
* **Action:**
  1. เข้าหน้า `/cart` ➔ คลิกปุ่ม *"Review reservation"*
  2. URL เข้าสู่ `/checkout` ตรวจสอบยอดรวมถูกต้อง
  3. คลิกปุ่ม *"Confirm reservation"*
* **Assertions:**
  * URL นำทางไปที่ `/checkout/confirmation?orderIds=...`
  * มีข้อความยืนยัน *"Reservation placed"* ปรากฏชัดเจน

### 🔹 TC-E2E-ORD-02: การดักจับ Order ID (Order ID Interception)
* **Action:** อ่านค่า Query Parameter `orderIds` จาก URL ของเบราว์เซอร์
* **Assertions:**
  * ได้รับค่า `orderId` ที่เป็นค่าจริง ไม่เป็นค่าว่างเปล่า

### 🔹 TC-E2E-ORD-03: การจัดส่งสินค้าผ่าน API (Seller API Fast-Forward)
* **Action:**
  * Cypress ยิงคำสั่ง `cy.sellerFastForwardOrder(createdOrderId, 'FULFILLED')`
  * เบื้องหลังใช้ JWT ของ `seller@mythix.com` ยิงคำขอ `PATCH /api/seller/orders/{orderId}/status`
* **Assertions:**
  * API ตอบกลับ Status 200 OK โดยไม่ต้องเปิดหน้าต่างใหม่หรือ Logout บัญชีผู้ซื้อ

### 🔹 TC-E2E-ORD-04: การยืนยันผลลัพธ์บนหน้าจอผู้ซื้อ (Status Verification)
* **Action:** คลิกปุ่ม *"View my orders"* เพื่อนำทางไปหน้า `/orders`
* **Assertions:**
  * หน้ารายการคำสั่งซื้อแสดง Order ที่เพิ่งสร้าง
  * Badge สถานะแสดงคำว่า **"Sent"** มีสีเขียว (`variant: success`) พร้อมไอคอนรถบรรทุก (`Truck`)

### 🔹 TC-E2E-ORD-05: การทำความสะอาดข้อมูล (Automated Data Teardown)
* **Action:** ทำงานอัตโนมัติใน Hook `afterEach()`
* **Implementation:**
  * เรียกคำสั่ง `cy.cleanTestOrder(createdOrderId)`
  * ทริกเกอร์ Cypress Task `cleanupLocalDbOrder` ใน `cypress.config.ts`
  * ส่งคำสั่ง SQL ไปลบระเบียนใน Docker PostgreSQL:
    ```sql
    DELETE FROM order_items WHERE order_id = :orderId;
    DELETE FROM orders WHERE id = :orderId;
    ```
* **Assertions:**
  * ไม่มีข้อมูลขยะตกค้างในตาราง และคืนสต็อกให้กับการ์ดใบเดิม ทำให้รันเทสซ้ำได้ไม่จำกัด

---

## 📋 4. ข้อมูลทางเทคนิคที่เกี่ยวข้อง (Technical References)

| จุดเชื่อมต่อ | Endpoint / คำสั่ง | บทบาท |
| :--- | :--- | :--- |
| **Buyer Checkout** | `POST /api/orders` | สั่งจองและสร้างคำสั่งซื้อ |
| **Seller Status** | `PATCH /api/seller/orders/{orderId}/status` | อัปเดตสถานะเป็น `FULFILLED` |
| **Buyer Order List** | `GET /api/orders/me` | ดึงรายการคำสั่งซื้อของผู้ซื้อ |
| **Teardown Task** | `docker exec tcg_postgres psql ...` | ล้างข้อมูลในตาราง `orders` และ `order_items` |

---

## 🔮 5. แผนการต่อขยายในอนาคต (Future Expansion)
* [ ] Sprint 4: เพิ่มการกรอก **Tracking Number (เลขพัสดุ)** ตอนผู้ขายกดยืนยันการจัดส่ง
* [ ] Sprint 4: การส่ง Notification / Email ผ่าน **Mailpit** แจ้งเตือนผู้ซื้อเมื่อสถานะเปลี่ยน
* [ ] การทดสอบ Flow การยกเลิกคำสั่งซื้อ (Cancel Order Journey) ทั้งฝั่งผู้ซื้อและผู้ขาย
