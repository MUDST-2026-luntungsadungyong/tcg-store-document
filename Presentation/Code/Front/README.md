# 🖥️ สารบัญโค้ดฝั่ง Frontend (Frontend Architecture & Code Index)
**โปรเจกต์:** Mythix TCG Store (Frontend Client)  
**สแต็กเทคโนโลยี:** React 18, TypeScript, Vite, Tailwind CSS, Zustand, Axios, Lucide Icons  
**ตำแหน่งซอร์สโค้ด:** `tcg-store-frontend/src`  
**เอกสารอ้างอิง:** [🏠 กลับสู่สารบัญรวม Presentation Code](../README.md) | [⚙️ ไปยังสารบัญฝั่ง Backend](../Back/README.md)

---

## 📌 บทนำและภาพรวมสถาปัตยกรรม (Architecture Overview)

ระบบ Frontend ของ **Mythix TCG Store** ได้รับการออกแบบตามแนวคิด **Feature-Driven Modular Architecture (Screaming Architecture)** เพื่อรองรับการขยายตัวของระบบในอนาคต โดยจัดแบ่งตามฟังก์ชันการทำงานทางธุรกิจ (Business Domains) มากกว่าการแบ่งตามประเภทของไฟล์ (เช่น Components, Pages) ทำให้โค้ดที่เกี่ยวข้องกับแต่ละฟีเจอร์เกาะกลุ่มกัน (High Cohesion) และลดการพึ่งพากันระหว่างโมดูล (Loose Coupling)

```
tcg-store-frontend/src/
├── app/             # แกนกลางของแอปพลิเคชัน (Router, Layouts, Global Providers)
├── features/        # โดเมนฟังก์ชันการทำงานหลัก (แยกโฟลเดอร์ตามโมดูลธุรกิจ)
├── entities/        # TypeScript Types, Domain Interfaces และ Business Rules สากล
├── shared/          # คอมโพเนนต์ UI พื้นฐาน, Network Client (Axios), และ Utilities
└── infrastructure/  # In-memory Mock Storage สำหรับพัฒนาและรัน Test แบบ Isolated
```

---

## 📑 สารบัญเอกสารเจาะลึกโค้ดและตัวอย่าง (Interactive Code Index)

ตารางด้านล่างนี้คือสารบัญที่เชื่อมโยงไปยังเอกสารอธิบายเชิงลึกและตัวอย่างโค้ดจริงในโฟลเดอร์ `examples/` สามารถคลิกที่ชื่อหัวข้อเพื่อเข้าชมเอกสารแต่ละส่วนได้ทันทีบน GitHub:

| หมวดหมู่ | ลิงก์เอกสารเจาะลึกและตัวอย่างโค้ด | โฟลเดอร์ซอร์สโค้ด | หน้าที่และความรับผิดชอบสำคัญ |
| :---: | :--- | :--- | :--- |
| **01** | [🌐 โครงสร้างหลักของแอปและการจัดการ Routing](./examples/01_app_and_routing.md) | `src/app/` | • จุดเริ่มของแอป (`main.tsx`, `App.tsx`)<br/>• การกำหนด Route ด้วย `createBrowserRouter`<br/>• การป้องกันสิทธิ์ด้วย `ProtectedRoute` (User, Seller, Admin)<br/>• ระบบ Layouts (Public, Workspace, Admin) |
| **02** | [🔐 ระบบยืนยันตัวตนและการจัดการสิทธิ์](./examples/02_auth_system.md) | `src/features/auth/` | • สถานะ Auth สากลด้วย Zustand (`useAuthStore`)<br/>• การซิงค์และกู้คืน LocalStorage Session Hydration<br/>• หน้า Login, Register, Forgot/Reset Password<br/>• ป็อปอัป `ContextualLoginModal` แบบไม่เปลี่ยนหน้า |
| **03** | [🎴 ตลาดการ์ด การค้นหา และตัวแสดงผล 3D](./examples/03_discovery_and_marketplace.md) | `src/features/discovery/` | • ตลาดการ์ด (`Marketplace.tsx`, `CardDetailPage.tsx`)<br/>• ตัวกรองหลายมิติ (`useFilterStore`: Rarity, Set, Condition)<br/>• ตัวแสดงผลการ์ด 3 มิติพร้อมแสงสะท้อน (`Card3DViewer.tsx`)<br/>• ตารางเปรียบเทียบข้อเสนอจากผู้ขายหลายราย |
| **04** | [🛒 ระบบตะกร้าสินค้าและการจอง](./examples/04_reservation_cart.md) | `src/features/reservation-cart/` | • จัดการตะกร้าสินค้าผ่าน `useReservationCartStore`<br/>• ตรวจสอบสต็อกคงเหลือแบบ Real-time<br/>• การเชื่อมต่อ API แบบ Dual-mode (Live API / Mock DB)<br/>• ตะกร้าแยกตามบัญชีผู้ใช้งานอย่างปลอดภัย |
| **05** | [💳 การสั่งซื้อและติดตามสถานะคำสั่งซื้อ](./examples/05_checkout_and_orders.md) | `src/features/checkout/`<br/>`src/features/reservations/` | • หน้ายืนยันการสั่งซื้อ (`CheckoutPage.tsx`)<br/>• การส่งคำสั่งซื้อแบบ Idempotent ป้องกันการกดซ้ำ<br/>• หน้ายืนยันการจอง (`ReservationConfirmationPage.tsx`)<br/>• หน้าประวัติและรายละเอียดออเดอร์ (`MyOrdersPage.tsx`) |
| **06** | [🏪 ระบบผู้ขายและการจัดการสต็อก](./examples/06_seller_portal.md) | `src/features/listings/`<br/>`src/features/stores/` | • หน้าคลังสินค้าของผู้ขาย (`ListingManagementPage.tsx`)<br/>• ตัวเลือกการ์ดจากฐานข้อมูลสากล (`CatalogCardPicker.tsx`)<br/>• การอัปโหลดรูปถ่ายสภาพจริงของการ์ด (Multipart Upload)<br/>• การตั้งค่าร้านค้าและหน้าร้านสาธารณะ (`PublicStorePage`) |
| **07** | [🏆 ระบบสะสมการ์ดและการจัดอันดับ](./examples/07_collection_and_leaderboard.md) | `src/features/collection/` | • แฟ้มสะสมการ์ดส่วนตัว (`MyCollectionPage.tsx`)<br/>• อัลกอริทึมคำนวณคะแนน Ultra-rare (`collectionScoring.ts`)<br/>• ตัวคูณคะแนนตามสภาพการ์ด (Condition Multipliers)<br/>• หน้าแชร์คอลเลกชันสาธารณะและ Leaderboard |
| **08** | [🛡️ ระบบจัดการหลังบ้านสำหรับผู้ดูแลระบบ](./examples/08_admin_backoffice.md) | `src/features/admin/` | • แดชบอร์ดสรุปสถิติแพลตฟอร์มและยอดขาย GMV<br/>• เวิร์กโฟลว์อนุมัติ/ปฏิเสธร้านค้า (`AdminSellerApplicationsPage`)<br/>• บันทึกความปลอดภัยที่ไม่สามารถลบได้ (`AdminAuditLogsPage`)<br/>• การจัดการผู้ใช้และคำสั่งซื้อทั้งระบบ |
| **09** | [🔔 ระบบการแจ้งเตือนและข้อมูลโปรไฟล์](./examples/09_notifications_and_profile.md) | `src/features/notifications/`<br/>`src/features/profile/` | • ศูนย์แจ้งเตือนแบบเรียลไทม์ (`NotificationsPage.tsx`)<br/>• Store ป้องกัน Race Condition ด้วย `requestGeneration`<br/>• หน้าจอโปรไฟล์และการแก้ไขข้อมูลการติดต่อ<br/>• แบบฟอร์มสมัครเปิดร้านค้าพร้อมระบบ KYC |
| **10** | [🧱 ส่วนประกอบ UI ส่วนกลางและโครงสร้างพื้นฐาน](./examples/10_shared_ui_and_infra.md) | `src/shared/`<br/>`src/infrastructure/` | • Axios Interceptors ดักจับ 401 และแนบ JWT Token อัตโนมัติ<br/>• Design System Components (`Button`, `Input`, `Modal`, `Toast`)<br/>• ระบบดักจับข้อผิดพลาด `ErrorBoundary`<br/>• In-memory Mock Storage สำหรับการทดสอบ E2E |

---

## 🔍 ตารางตรวจสอบความครบถ้วน 100% (Full Component & Page Inventory)

เพื่อให้มั่นใจว่าไม่มีโค้ดส่วนใดตกหล่น ตารางนี้ตรวจสอบและแมปปิ้ง **ทุก Page (31 หน้า), ทุก Zustand Store (6 สโตร์), และทุก API Client (11 โมดูล)** ในระบบ Frontend:

### 1. หน้าจอทั้งหมด (All 31 Frontend Pages)
| ลำดับ | หน้าจอ (Page Component) | โฟลเดอร์ตำแหน่งที่ตั้ง | เอกสารที่อธิบายและมีตัวอย่างโค้ด |
| :---: | :--- | :--- | :--- |
| 1 | `HomePage.tsx` | `features/discovery/pages/` | [03: Discovery & 3D Viewer](./examples/03_discovery_and_marketplace.md) |
| 2 | `Marketplace.tsx` | `features/discovery/pages/` | [03: Discovery & 3D Viewer](./examples/03_discovery_and_marketplace.md) |
| 3 | `CardDiscoveryPage.tsx` | `features/discovery/pages/` | [03: Discovery & 3D Viewer](./examples/03_discovery_and_marketplace.md) |
| 4 | `CardDetailPage.tsx` | `features/discovery/pages/` | [03: Discovery & 3D Viewer](./examples/03_discovery_and_marketplace.md) |
| 5 | `LoginPage.tsx` | `features/auth/pages/` | [02: Authentication & State](./examples/02_auth_system.md) |
| 6 | `SignUpPage.tsx` | `features/auth/pages/` | [02: Authentication & State](./examples/02_auth_system.md) |
| 7 | `ForgotPasswordPage.tsx` | `features/auth/pages/` | [02: Authentication & State](./examples/02_auth_system.md) |
| 8 | `ResetPasswordPage.tsx` | `features/auth/pages/` | [02: Authentication & State](./examples/02_auth_system.md) |
| 9 | `ResetSuccessPage.tsx` | `features/auth/pages/` | [02: Authentication & State](./examples/02_auth_system.md) |
| 10 | `CheckEmailPage.tsx` | `features/auth/pages/` | [02: Authentication & State](./examples/02_auth_system.md) |
| 11 | `ReservationCartPage.tsx` | `features/reservation-cart/pages/` | [04: Reservation Cart System](./examples/04_reservation_cart.md) |
| 12 | `CheckoutPage.tsx` | `features/checkout/pages/` | [05: Checkout & Orders](./examples/05_checkout_and_orders.md) |
| 13 | `ReservationConfirmationPage.tsx` | `features/checkout/pages/` | [05: Checkout & Orders](./examples/05_checkout_and_orders.md) |
| 14 | `MyOrdersPage.tsx` | `features/reservations/pages/` | [05: Checkout & Orders](./examples/05_checkout_and_orders.md) |
| 15 | `OrderDetailPage.tsx` | `features/reservations/pages/` | [05: Checkout & Orders](./examples/05_checkout_and_orders.md) |
| 16 | `SellerOrdersPage.tsx` | `features/reservations/pages/` | [05: Checkout & Orders](./examples/05_checkout_and_orders.md) |
| 17 | `ListingManagementPage.tsx` | `features/listings/pages/` | [06: Seller Portal & Inventory](./examples/06_seller_portal.md) |
| 18 | `PublicStorePage.tsx` | `features/stores/pages/` | [06: Seller Portal & Inventory](./examples/06_seller_portal.md) |
| 19 | `StoreSettingsPage.tsx` | `features/stores/pages/` | [06: Seller Portal & Inventory](./examples/06_seller_portal.md) |
| 20 | `MyCollectionPage.tsx` | `features/collection/pages/` | [07: Collection & Scoring](./examples/07_collection_and_leaderboard.md) |
| 21 | `PublicCollectionPage.tsx` | `features/collection/pages/` | [07: Collection & Scoring](./examples/07_collection_and_leaderboard.md) |
| 22 | `AdminDashboardPage.tsx` | `features/admin/pages/` | [08: Admin Backoffice](./examples/08_admin_backoffice.md) |
| 23 | `AdminSellerApplicationsPage.tsx` | `features/admin/pages/` | [08: Admin Backoffice](./examples/08_admin_backoffice.md) |
| 24 | `AdminUsersPage.tsx` | `features/admin/pages/` | [08: Admin Backoffice](./examples/08_admin_backoffice.md) |
| 25 | `AdminOrderLogsPage.tsx` | `features/admin/pages/` | [08: Admin Backoffice](./examples/08_admin_backoffice.md) |
| 26 | `AdminAuditLogsPage.tsx` | `features/admin/pages/` | [08: Admin Backoffice](./examples/08_admin_backoffice.md) |
| 27 | `NotificationsPage.tsx` | `features/notifications/pages/` | [09: Notifications & Profile](./examples/09_notifications_and_profile.md) |
| 28 | `ProfilePage.tsx` | `features/profile/pages/` | [09: Notifications & Profile](./examples/09_notifications_and_profile.md) |
| 29 | `EditProfilePage.tsx` | `features/profile/pages/` | [09: Notifications & Profile](./examples/09_notifications_and_profile.md) |
| 30 | `EditContactInformationPage.tsx` | `features/profile/pages/` | [09: Notifications & Profile](./examples/09_notifications_and_profile.md) |
| 31 | `SellerApplicationPage.tsx` | `features/profile/pages/` | [09: Notifications & Profile](./examples/09_notifications_and_profile.md) |

### 2. Global State Stores ทั้งหมด (All 6 Zustand Stores)
| ชื่อ Store | หน้าที่และข้อมูลที่เก็บ | เอกสารที่อธิบาย |
| :--- | :--- | :--- |
| `useAuthStore.ts` | JWT Token, ข้อมูล User, LocalStorage Hydration, Login/Logout | [02: Authentication & State](./examples/02_auth_system.md) |
| `useFilterStore.ts` | Search Query, Rarity, Set, Price Range, Sort Order | [03: Discovery & 3D Viewer](./examples/03_discovery_and_marketplace.md) |
| `useReservationCartStore.ts` | รายการการ์ดในตะกร้า, รวมจำนวน, คำนวณยอดเงิน, ซิงค์ API | [04: Reservation Cart System](./examples/04_reservation_cart.md) |
| `useListingStore.ts` | สต็อกและรายการลงขายของผู้ขาย, ตัวกรองสถานะ Active/Draft | [06: Seller Portal & Inventory](./examples/06_seller_portal.md) |
| `useNotificationStore.ts` | Unread Count Badge, Generation Counter ป้องกัน Race Condition | [09: Notifications & Profile](./examples/09_notifications_and_profile.md) |
| `useToastStore.ts` | ข้อความแจ้งเตือน Pop-up Feedback คิวของระบบ | [10: Shared UI & Infra](./examples/10_shared_ui_and_infra.md) |

---

## 💡 ไฮไลต์และแนวทางปฏิบัติทางวิศวกรรม (Engineering Highlights)

1. **State Isolation with Zustand:** แยก State ออกตามขอบเขตของ Feature ทำให้แต่ละ Store มีขนาดกะทัดรัด ไม่เกิด Re-render ที่ไม่จำเป็น และง่ายต่อการเขียน Unit Test
2. **Deterministic Route Guarding:** `ProtectedRoute` ทำหน้าที่ตรวจสอบทั้ง JWT Token, บทบาทผู้ใช้งาน (`Role.ADMIN`), และสถานะการเป็นผู้ขาย (`sellerStatus = 'APPROVED'`) อย่างรัดกุมก่อนอนุญาตให้ Render หน้าจอ
3. **Pure CSS 3D Card Simulation:** `Card3DViewer` ใช้การคำนวณคณิตศาสตร์พิกัดเมาส์ร่วมกับ CSS 3D Transforms (`perspective`, `rotateX`, `rotateY`, `radial-gradient`) เพื่อสร้างการ์ดโฮโลแกรมสามมิติที่เบาและรวดเร็วกว่า WebGL
4. **Resilient Local Caching & Hydration:** จัดการเรื่องความคงอยู่ของ Session ผู้ใช้ผ่านการทำ Storage Hydration ที่ป้องกันปัญหาหน้าจอวูบวาบ (Flicker-free Loading)
5. **Full Test Pyramid Readiness:** ออกแบบโค้ดทุกส่วนให้สามารถทดสอบได้อย่างสมบูรณ์ โดยโปรเจกต์นี้มี Unit & Component Tests มากกว่า **234 Tests (Vitest)** และ E2E Journeys อีก **99 Tests (Cypress)**
