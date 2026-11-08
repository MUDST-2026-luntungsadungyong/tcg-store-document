# ⚙️ สารบัญโค้ดฝั่ง Backend (Backend Architecture & Code Index)
**โปรเจกต์:** Mythix TCG Store (Backend API Platform)  
**สแต็กเทคโนโลยี:** Java 21, Spring Boot 3.3.x, Spring Security 6, Spring Data JPA / Hibernate, TiDB Cloud / PostgreSQL Neon, Maven  
**ตำแหน่งซอร์สโค้ด:** `tcg-store-backend/src/main/java/com/mythix/tcg`  
**เอกสารอ้างอิง:** [🏠 กลับสู่สารบัญรวม Presentation Code](../README.md) | [🖥️ ไปยังสารบัญฝั่ง Frontend](../Front/README.md)

---

## 📌 บทนำและภาพรวมสถาปัตยกรรม (Architecture Overview)

ระบบ Backend ของ **Mythix TCG Store** ได้รับการพัฒนาด้วยภาษา Java 21 และ Spring Boot 3 โดยยึดหลัก **Clean Layered Architecture (DDD - Domain Driven Design)** ซึ่งแบ่งแยกความรับผิดชอบอย่างชัดเจนในแต่ละแพ็กเกจ (Layered Separation of Concerns):
- **API Layer (`/api`):** REST Controllers, DTOs (Request/Response Records), และ Input Validation (@Valid)
- **Application Layer (`/application`):** Domain Services, Transaction Boundaries (@Transactional), และ Business Rule Exceptions
- **Domain Layer (`/domain`):** JPA Entities, Enums, และ Core Business Logic
- **Persistence Layer (`/persistence`):** Spring Data JPA Repositories, Custom JPQL Queries, และ Pessimistic Locks

```
com.mythix.tcg/
├── common/        # สถาปัตยกรรมความปลอดภัยระดับสากล, JWT Filters, Global Exception Handler
├── auth/          # วงจรชีวิตการยืนยันตัวตน, ออก JWT Token, จัดการรหัสผ่าน BCrypt
├── user/          # โดเมนผู้ใช้งาน, โปรไฟล์, และสิทธิ์การเข้าถึง (RBAC)
├── catalog/       # คลังการ์ดแม่แบบมาตรฐานสากล (Master Card Catalog)
├── product/       # สินค้าที่ร้านค้านำมาวางขายในตลาด (Marketplace Product Listings)
├── cart/          # ระบบตะกร้าสินค้าของผู้ซื้อและการตรวจสอบสต็อกเบื้องต้น
├── order/         # หัวใจสำคัญของระบบสั่งซื้อ: Pessimistic Lock, Deadlock Prevention, Idempotency
├── seller/        # ระบบยืนยันตัวตนร้านค้า (KYC) และการอัปโหลดรูปภาพสภาพการ์ดจริง
├── collection/    # แฟ้มสะสมการ์ดดิจิทัลส่วนตัวและ Showcase สาธารณะ
├── admin/         # ระบบหลังบ้านของผู้ดูแลระบบ, การอนุมัติผู้ขาย และ Immutable Audit Logs
└── notification/  # ศูนย์กระจายการแจ้งเตือนภายในระบบ (In-app Notification Engine)
```

---

## 📑 สารบัญเอกสารเจาะลึกโค้ดและตัวอย่าง (Interactive Code Index)

ตารางด้านล่างนี้คือสารบัญที่เชื่อมโยงไปยังเอกสารอธิบายเชิงลึกและตัวอย่างโค้ดจริงในโฟลเดอร์ `examples/` สามารถคลิกที่ชื่อหัวข้อเพื่อเข้าชมเอกสารแต่ละส่วนได้ทันทีบน GitHub:

| หมวดหมู่ | ลิงก์เอกสารเจาะลึกและตัวอย่างโค้ด | โฟลเดอร์ซอร์สโค้ด | หน้าที่และความรับผิดชอบสำคัญ |
| :---: | :--- | :--- | :--- |
| **01** | [🛡️ สถาปัตยกรรมความปลอดภัยและการจัดการข้อผิดพลาด](./examples/01_architecture_and_security.md) | `com/mythix/tcg/common/` | • Spring Security 6 Stateless Filter Chain<br/>• การตรวจสอบ JWT Token (`JwtAuthenticationFilter`)<br/>• การตรวจสอบสิทธิ์ร้านค้าแบบ Real-time (`SellerInventoryAuthorizationManager`)<br/>• Global Exception Handler คืนข้อผิดพลาดตามมาตรฐาน RFC 7807 |
| **02** | [🔑 ระบบสมาชิกและการจัดการสิทธิ์](./examples/02_auth_and_user_domain.md) | `com/mythix/tcg/auth/`<br/>`com/mythix/tcg/user/` | • การลงทะเบียนและตรวจสอบอีเมลซ้ำ<br/>• การเข้ารหัสผ่านด้วย BCrypt (10 rounds)<br/>• การออกและตรวจสอบ JWT Token (`JwtService`)<br/>• โมเดลข้อมูลผู้ใช้ `User` Entity และโปรไฟล์ส่วนตัว |
| **03** | [📦 คลังการ์ดแม่แบบและสินค้าในตลาด](./examples/03_catalog_and_products.md) | `com/mythix/tcg/catalog/`<br/>`com/mythix/tcg/product/` | • Master Catalog การ์ดสากล (`CardController`, `Card`)<br/>• ระบบค้นหาและตัวกรองสินค้าหลายมิติ (JPA Specification)<br/>• การทำ Pagination และการเรียงลำดับราคา<br/>• เมธอด `findByIdForUpdate` รองรับการล็อคสินค้า |
| **04** | [🛒 ระบบจัดการตะกร้าสินค้า](./examples/04_cart_system.md) | `com/mythix/tcg/cart/` | • ตะกร้าสินค้าผูกกับฐานข้อมูลจริง (Cross-device Persistence)<br/>• การตรวจสอบสต็อกสินค้าแบบ Real-time ตอนกดเพิ่ม<br/>• ป้องกันผู้ขายซื้อการ์ดจากร้านของตัวเอง (`CartNotAllowedException`)<br/>• ป้องกันปัญหาตัวเลขล้น (Integer Overflow Protection) |
| **05** | [⚡ การสั่งซื้อและทรานแซกชันแบบ Pessimistic Locking](./examples/05_order_and_checkout_flow.md) | `com/mythix/tcg/order/` | • **Pessimistic Write Lock (`PESSIMISTIC_WRITE`)** ป้องกันสต็อกติดลบ<br/>• **Deadlock Avoidance** ด้วยการจัดเรียงลำดับ ID สินค้า<br/>• ป้องกันการกดซื้อซ้ำด้วย **Idempotency-Key Header**<br/>• การตัดแบ่งออเดอร์ตามผู้ขาย (Multi-seller Order Split) |
| **06** | [🏬 ระบบผู้ขายและการยืนยันตัวตน](./examples/06_seller_and_verification.md) | `com/mythix/tcg/seller/` | • เวิร์กโฟลว์ยื่นขอเปิดร้านค้า (Seller Verification / KYC)<br/>• จัดการสินค้าของผู้ขาย พร้อมบล็อคการลบหากมีออเดอร์ค้าง<br/>• ตรวจสอบไฟล์ภาพจาก **Magic Bytes** (PNG/JPEG Signature)<br/>• ลบไฟล์ภาพอัตโนมัติหาก Database Rollback (`TransactionSynchronization`) |
| **07** | [📚 ระบบจัดการคอลเลกชันการ์ดสะสม](./examples/07_collection_management.md) | `com/mythix/tcg/collection/` | • แฟ้มสะสมการ์ดส่วนตัวของนักสะสม (`CollectionItem`)<br/>• การรวมจำนวนการ์ดซ้ำอัตโนมัติ (Deduplication)<br/>• การเปิดให้บุคคลทั่วไปเข้าชมแฟ้มสะสมอย่างปลอดภัย (`PublicCollectionService`)<br/>• Compound Unique Constraint ป้องกันข้อมูลซ้ำ |
| **08** | [👑 การบริหารจัดการและระบบบันทึกความปลอดภัย](./examples/08_admin_management.md) | `com/mythix/tcg/admin/` | • เวิร์กโฟลว์อนุมัติ/ปฏิเสธร้านค้า พร้อมล็อคใบสมัคร<br/>• บันทึกหลักฐานกิจกรรมความปลอดภัยที่ไม่สามารถลบได้ (`AuditLog`)<br/>• สถิติและตัวชี้วัดแพลตฟอร์ม (GMV, จำนวนผู้ใช้, คำสั่งซื้อ)<br/>• การส่งสัญญาณแจ้งเตือนอัตโนมัติเมื่ออนุมัติผล |
| **09** | [🔔 ระบบบริการแจ้งเตือนผู้ใช้งาน](./examples/09_notification_service.md) | `com/mythix/tcg/notification/` | • ศูนย์กลางกระจายการแจ้งเตือนภายในระบบ (`NotificationService`)<br/>• กำหนดประเภทเหตุการณ์มาตรฐาน (`NotificationTypes`)<br/>• นับจำนวนข้อความที่ยังไม่อ่านอย่างรวดเร็วด้วย DB Index<br/>• การเปลี่ยนสถานะอ่านแล้วรายข้อความหรืออ่านทั้งหมด |
| **10** | [🗄️ สถาปัตยกรรมฐานข้อมูลและโครงสร้างพื้นฐานคลาวด์](./examples/10_database_and_infra.md) | `src/main/resources/`<br/>Root Dockerfile | • รองรับการสลับระหว่าง TiDB Cloud (MySQL Engine) และ PostgreSQL Neon<br/>• การปรับแต่ง HikariCP Connection Pool สำหรับ Serverless DB<br/>• บังคับตรวจสอบ Schema อย่างเข้มงวดด้วย `ddl-auto: validate`<br/>• Dockerfile แบบ Multi-stage รันด้วย Non-root user (`spring:spring`) |

---

## 🔍 ตารางตรวจสอบความครบถ้วน 100% (Full Backend Architecture Inventory)

เพื่อให้มั่นใจว่าครอบคลุมสถาปัตยกรรมทั้งระบบ ตารางนี้ตรวจสอบและแมปปิ้ง **ทุก Controller (20 ตัว), ทุก Service (15 ตัว), ทุก Repository (13 ตัว), และทุก Entity (11 ตัว)** ใน Backend:

### 1. REST Controllers ทั้งหมด (All 20 Controllers - 100% Coverage)
| ลำดับ | Controller Class | Path Base | หน้าที่หลัก | เอกสารที่อธิบาย |
| :---: | :--- | :--- | :--- | :--- |
| 1 | `AuthController` | `/api/auth` | สมัครสมาชิก, ล็อกอิน, ขอรีเซ็ตรหัสผ่าน, ดึงโปรไฟล์ me | [02: Auth & User Domain](./examples/02_auth_and_user_domain.md) |
| 2 | `UserProfileController` | `/api/users/profile` | ดูและแก้ไขข้อมูลส่วนตัว ที่อยู่ และเบอร์โทร | [02: Auth & User Domain](./examples/02_auth_and_user_domain.md) |
| 3 | `CardController` | `/api/cards` | ค้นหาข้อมูลการ์ดต้นแบบจาก Master Catalog | [03: Catalog & Products](./examples/03_catalog_and_products.md) |
| 4 | `ProductController` | `/api/products` | ค้นหาและกรองสินค้าในตลาด Marketplace | [03: Catalog & Products](./examples/03_catalog_and_products.md) |
| 5 | `CartController` | `/api/cart` | จัดการตะกร้าสินค้า เพิ่ม/ลด/ลบ และตรวจสต็อก | [04: Cart System](./examples/04_cart_system.md) |
| 6 | `CheckoutController` | `/api/checkout` | รับคำสั่งซื้อ Idempotent Checkout และตัดสต็อก | [05: Order & Pessimistic Lock](./examples/05_order_and_checkout_flow.md) |
| 7 | `BuyerOrderController` | `/api/orders/buyer` | ดูประวัติคำสั่งซื้อและขอยกเลิกออเดอร์ของผู้ซื้อ | [05: Order & Pessimistic Lock](./examples/05_order_and_checkout_flow.md) |
| 8 | `SellerOrderController` | `/api/orders/seller` | ดูรายการสั่งซื้อและอัปเดตสถานะจัดส่งของร้านค้า | [05: Order & Pessimistic Lock](./examples/05_order_and_checkout_flow.md) |
| 9 | `SellerProductController` | `/api/seller/products` | เพิ่ม แก้ไข ลบ และเปลี่ยนสถานะสินค้าของผู้ขาย | [06: Seller & Verification](./examples/06_seller_and_verification.md) |
| 10 | `SellerVerificationController` | `/api/seller/verification` | ส่งเอกสารคำขอเปิดร้านค้า (KYC Application) | [06: Seller & Verification](./examples/06_seller_and_verification.md) |
| 11 | `StoreProfileController` | `/api/stores` | แก้ไขข้อมูลร้านค้า และแสดงหน้าร้านสาธารณะ | [06: Seller & Verification](./examples/06_seller_and_verification.md) |
| 12 | `CollectionController` | `/api/collections` | จัดการแฟ้มสะสมการ์ดส่วนบุคคล | [07: Collection Management](./examples/07_collection_management.md) |
| 13 | `PublicCollectionController` | `/api/collections/public` | แสดงคอลเลกชันสาธารณะสำหรับแชร์ | [07: Collection Management](./examples/07_collection_management.md) |
| 14 | `AdminSellerController` | `/api/admin/sellers` | อนุมัติหรือปฏิเสธคำขอเปิดร้านค้า | [08: Admin & Audit Logs](./examples/08_admin_management.md) |
| 15 | `AdminDashboardController` | `/api/admin/dashboard` | รวมสถิติ GMV, ผู้ใช้, สต็อก, คำสั่งซื้อ | [08: Admin & Audit Logs](./examples/08_admin_management.md) |
| 16 | `AdminAuditLogsController` | `/api/admin/audit-logs` | แสดงประวัติการกระทำของผู้ดูแลระบบ | [08: Admin & Audit Logs](./examples/08_admin_management.md) |
| 17 | `AdminUsersController` | `/api/admin/users` | ตรวจสอบและบริหารจัดการสมาชิก | [08: Admin & Audit Logs](./examples/08_admin_management.md) |
| 18 | `AdminOrderLogsController` | `/api/admin/order-logs` | ตรวจสอบบันทึกสถานะคำสั่งซื้อทั้งระบบ | [08: Admin & Audit Logs](./examples/08_admin_management.md) |
| 19 | `NotificationController` | `/api/notifications` | ดึงแจ้งเตือน นับจำนวน Unread และปรับสถานะอ่าน | [09: Notification Service](./examples/09_notification_service.md) |
| 20 | `HealthController` | `/api/health` | ตรวจสอบความพร้อมของระบบสำหรับ Load Balancer | [01: Security & Exceptions](./examples/01_architecture_and_security.md) |

### 2. Domain Services ทั้งหมด (All 15 Services)
| ชื่อ Service Class | ขอบเขตความรับผิดชอบ | เอกสารที่อธิบาย |
| :--- | :--- | :--- |
| `AuthService` | ตรวจสอบรหัสผ่าน, สมัครสมาชิก, ออก JWT Token | [02: Auth & User Domain](./examples/02_auth_and_user_domain.md) |
| `JwtService` | สร้าง Sign และถอดรหัสตรวจสอบความถูกต้องของ JWT | [02: Auth & User Domain](./examples/02_auth_and_user_domain.md) |
| `UserProfileService` | อัปเดตข้อมูลส่วนตัวผู้ใช้ | [02: Auth & User Domain](./examples/02_auth_and_user_domain.md) |
| `ProductService` | ค้นหาสินค้าด้วย Dynamic JPA Specification | [03: Catalog & Products](./examples/03_catalog_and_products.md) |
| `CartService` | จัดการและตรวจสอบสต็อกสินค้าในตะกร้า | [04: Cart System](./examples/04_cart_system.md) |
| `CheckoutService` | **Pessimistic Locking**, Deadlock Prevention, หักสต็อก | [05: Order & Pessimistic Lock](./examples/05_order_and_checkout_flow.md) |
| `BuyerOrderService` | ตรวจสอบประวัติและยกเลิกออเดอร์ฝั่งผู้ซื้อ | [05: Order & Pessimistic Lock](./examples/05_order_and_checkout_flow.md) |
| `SellerOrderService` | ปรับสถานะจัดส่งและ Tracking Number ฝั่งผู้ขาย | [05: Order & Pessimistic Lock](./examples/05_order_and_checkout_flow.md) |
| `SellerProductService` | CRUD สินค้าของผู้ขาย และป้องกันการลบสินค้ามีออเดอร์ | [06: Seller & Verification](./examples/06_seller_and_verification.md) |
| `SellerVerificationService`| บันทึกคำขอเปิดร้านค้าเข้าสู่สถานะ PENDING | [06: Seller & Verification](./examples/06_seller_and_verification.md) |
| `StoreProfileService` | จัดการชื่อและข้อมูลติดต่อของร้านค้า | [06: Seller & Verification](./examples/06_seller_and_verification.md) |
| `SellerProductImageStorage`| ตรวจสอบ **Magic Bytes**, สุ่ม UUID, และ Transaction Sync | [06: Seller & Verification](./examples/06_seller_and_verification.md) |
| `CollectionService` | แฟ้มสะสมการ์ดส่วนตัว และทบจำนวนการ์ดซ้ำ | [07: Collection Management](./examples/07_collection_management.md) |
| `PublicCollectionService` | แปลงข้อมูลคอลเลกชันสำหรับแชร์โดยไม่รั่วไหลข้อมูลส่วนบุคคล | [07: Collection Management](./examples/07_collection_management.md) |
| `AdminSellerService` | ตรวจสอบใบสมัคร ล็อคแถว อนุมัติ และบันทึก Audit Log | [08: Admin & Audit Logs](./examples/08_admin_management.md) |
| `NotificationService` | ศูนย์กลางกระจายแจ้งเตือน (Event Dispatcher) | [09: Notification Service](./examples/09_notification_service.md) |

### 3. JPA Repositories ทั้งหมด (All 13 Repositories)
| ชื่อ Repository | Entity ที่จัดการ | คุณสมบัติพิเศษที่นำเสนอ |
| :--- | :--- | :--- |
| `UserRepository` | `User` | `findByIdForUpdate` (Pessimistic Lock ผู้ซื้อ), `findByEmail` |
| `ProductRepository` | `Product` | `findByIdForUpdate` (Pessimistic Write Lock สินค้า), JPA Specification |
| `CartItemRepository` | `CartItem` | `findAllByUserIdWithProduct` (Fetch Join ป้องกัน N+1) |
| `OrderRepository` | `Order` | `findByIdempotencyKey`, `paidOrFulfilledGmv` |
| `OrderItemRepository` | `OrderItem` | บันทึกประวัติรายการสินค้าและราคา Snapshot |
| `OrderStatusLogRepository` | `OrderStatusLog` | บันทึกการเปลี่ยนสถานะของคำสั่งซื้อทุกขั้นตอน |
| `CardRepository` | `Card` | จัดการ Master Catalog การ์ดมาตรฐาน |
| `CollectionRepository` | `CollectionItem` | ค้นหาด้วย Compound Unique `(userId, cardId, condition, language)` |
| `SellerProfileRepository`| `SellerProfile` | `findByIdForUpdate` ล็อคใบสมัครตอนแอดมินตรวจ |
| `SellerProductRepository`| `Product` | ตรวจสอบความเป็นเจ้าของสินค้าของผู้ขาย |
| `SellerOrderItemRepository`| `OrderItem` | `existsActiveOrdersForProduct` บล็อคการลบสินค้าที่ติดออเดอร์ |
| `AuditLogRepository` | `AuditLog` | จัดเก็บหลักฐานความปลอดภัยแบบ Append-only |
| `NotificationRepository` | `UserNotification`| `countByUserIdAndReadAtIsNull` นับ Unread รวดเร็วด้วย DB Index |

### 4. JPA Domain Entities ทั้งหมด (All 11 Entities)
`User`, `Card`, `Product`, `CartItem`, `Order`, `OrderItem`, `OrderStatusLog`, `SellerProfile`, `CollectionItem`, `AuditLog`, `UserNotification`

---

## 💡 ไฮไลต์และแนวทางปฏิบัติทางวิศวกรรม (Engineering Highlights)

1. **Enterprise Concurrency Protection:** ออกแบบการตัดสต็อกสินค้าในขั้นตอนการ Checkout โดยใช้ **Pessimistic Locking (`SELECT ... FOR UPDATE`)** ควบคู่ไปกับการจัดเรียงลำดับ ID (`sorted()`) ซึ่งเป็นการันตีว่าจะไม่เกิด Deadlock และไม่มีปัญหาสต็อกติดลบหรือ Overselling แม้จะมีการสั่งซื้อพร้อมกันจากผู้ใช้หลายร้อยคน
2. **Idempotency Execution Guarantee:** มีระบบดักจับ Header `Idempotency-Key` เพื่อให้การทำงานในขั้นตอนที่เสี่ยง เช่น การตัดเงินหรือการสร้าง Order เป็นแบบ Idempotent หากเน็ตหลุดแล้ว Client กดส่งซ้ำ ระบบจะคืนผลลัพธ์ของคำสั่งซื้อเดิมกลับไปทันที ไม่มีการทำซ้ำ
3. **Defense-in-Depth Security:** ป้องกันการโจมตีตั้งแต่ระดับ Network (CORS Whitelist), Transport (Stateless JWT Filter), Authorization (Dynamic DB Check), และ File Upload (Magic Bytes Signature Inspection ไม่เชื่อถือ Extension หลอก)
4. **Cloud-Native Database Flexibility:** โครงสร้าง JPA Entity ถูกเขียนให้เข้ากันได้ทั้งกับ **TiDB Cloud (Distributed NewSQL MySQL Dialect)** และ **PostgreSQL Neon (Serverless Postgres)** สามารถเปลี่ยน Cloud Provider ได้เพียงแค่สลับ Active Profile
5. **Robust Test Coverage:** ผ่านการทดสอบระดับ Unit Tests และ Controller Integration Tests ด้วย JUnit 5 และ MockMvc รวมทั้งสิ้นมากกว่า **185+ Tests (ครอบคลุม Controller 20/20 ตัว 100%)**
