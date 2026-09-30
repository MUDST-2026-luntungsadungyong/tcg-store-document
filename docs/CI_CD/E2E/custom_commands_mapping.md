# ðŸ§© à¹à¸œà¸™à¸œà¸±à¸‡à¸„à¸§à¸²à¸¡à¸ªà¸±à¸¡à¸žà¸±à¸™à¸˜à¹Œ Custom Commands à¸à¸±à¸š Core User Journeys (Commands Matrix)

> **à¹„à¸Ÿà¸¥à¹Œà¸•à¹‰à¸™à¸—à¸²à¸‡:** [`Code_devops/tcg-store-frontend/cypress/support/commands.ts`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/support/commands.ts)  
> **à¸«à¸¡à¸§à¸”à¸«à¸¡à¸¹à¹ˆ:** `CI_CD / E2E / Custom Commands Reference`  
> **à¸§à¸±à¸•à¸–à¸¸à¸›à¸£à¸°à¸ªà¸‡à¸„à¹Œ:** à¹à¸ªà¸”à¸‡à¸à¸²à¸£à¸ˆà¸±à¸šà¸„à¸¹à¹ˆ (Mapping) à¸£à¸°à¸«à¸§à¹ˆà¸²à¸‡à¸„à¸³à¸ªà¸±à¹ˆà¸‡à¹€à¸ªà¸£à¸´à¸¡à¸‚à¸­à¸‡ Cypress à¸à¸±à¸šà¹à¸•à¹ˆà¸¥à¸° User Journey à¸—à¸µà¹ˆà¹€à¸£à¸µà¸¢à¸à¹ƒà¸Šà¹‰à¸‡à¸²à¸™

---

## ðŸ“Š 1. à¸•à¸²à¸£à¸²à¸‡à¸„à¸§à¸²à¸¡à¸ªà¸±à¸¡à¸žà¸±à¸™à¸˜à¹Œà¸«à¸¥à¸±à¸ (Commands-to-Journeys Matrix)

| à¸„à¸³à¸ªà¸±à¹ˆà¸‡ Custom Command | à¸žà¸²à¸£à¸²à¸¡à¸´à¹€à¸•à¸­à¸£à¹Œà¸—à¸µà¹ˆà¸£à¸±à¸š | à¸§à¸±à¸•à¸–à¸¸à¸›à¸£à¸°à¸ªà¸‡à¸„à¹Œà¸à¸²à¸£à¸—à¸³à¸‡à¸²à¸™ | Core User Journeys à¸—à¸µà¹ˆà¹ƒà¸Šà¹‰à¸‡à¸²à¸™à¸›à¸±à¸ˆà¸ˆà¸¸à¸šà¸±à¸™ | à¸£à¸­à¸‡à¸£à¸±à¸š Use Cases à¹ƒà¸™à¸­à¸™à¸²à¸„à¸• |
| :--- | :--- | :--- | :--- | :--- |
| **`cy.loginByMock()`** | `role?: 'CUSTOMER' \| 'SELLER' \| 'ADMIN'` | à¸ˆà¸³à¸¥à¸­à¸‡ Authentication State à¸¥à¸‡à¹ƒà¸™ `localStorage` (`token` à¹à¸¥à¸° `tcg_auth_storage`) à¹‚à¸”à¸¢à¹„à¸¡à¹ˆà¸•à¹‰à¸­à¸‡à¸žà¸´à¸¡à¸žà¹Œà¸Ÿà¸­à¸£à¹Œà¸¡à¸¥à¹‡à¸­à¸à¸­à¸´à¸™ | ðŸ”¹ **Journey 3** (Cart & Inventory)<br>ðŸ”¹ **Journey 4** (Order Lifecycle) | â€¢ à¸ˆà¸±à¸”à¸à¸²à¸£à¸£à¹‰à¸²à¸™à¸„à¹‰à¸²à¸œà¸¹à¹‰à¸‚à¸²à¸¢ (Seller Workspace)<br>â€¢ à¸­à¸™à¸¸à¸¡à¸±à¸•à¸´ KYC à¸œà¸¹à¹‰à¸‚à¸²à¸¢ (Admin Dashboard)<br>â€¢ à¸„à¸­à¸¥à¹€à¸¥à¸à¸Šà¸±à¸™à¸ªà¹ˆà¸§à¸™à¸•à¸±à¸§ (My Collection) |
| **`cy.loginByUI()`** | `email: string`, `password?: string` | à¸à¸£à¸­à¸à¸Ÿà¸­à¸£à¹Œà¸¡à¸¥à¹‡à¸­à¸à¸­à¸´à¸™à¸ˆà¸£à¸´à¸‡à¸œà¹ˆà¸²à¸™à¸«à¸™à¹‰à¸²à¸ˆà¸­à¹€à¸šà¸£à¸²à¸§à¹Œà¹€à¸‹à¸­à¸£à¹Œ (`#email`, `#password`) à¹à¸¥à¹‰à¸§à¸à¸” Submit | ðŸ”¹ **Journey 2** (Flow 2.1 Login) | â€¢ à¸à¸²à¸£à¸—à¸”à¸ªà¸­à¸š UI Login End-to-End |
| **`cy.registerNewUser()`** | `name, email, password` | à¸à¸£à¸­à¸à¸Ÿà¸­à¸£à¹Œà¸¡à¸ªà¸¡à¸±à¸„à¸£à¸ªà¸¡à¸²à¸Šà¸´à¸à¹ƒà¸«à¸¡à¹ˆà¸„à¸£à¸š 4 à¸Šà¹ˆà¸­à¸‡à¸—à¸µà¹ˆà¸«à¸™à¹‰à¸² `/register` à¹à¸¥à¹‰à¸§à¸à¸” Submit | ðŸ”¹ **Journey 2** (Flow 2.2 Sign-Up) | â€¢ Onboarding Tour à¸œà¸¹à¹‰à¹ƒà¸Šà¹‰à¹ƒà¸«à¸¡à¹ˆ |
| **`cy.requestPasswordReset()`**| `email: string` | à¸à¸£à¸­à¸à¸­à¸µà¹€à¸¡à¸¥à¸—à¸µà¹ˆà¸«à¸™à¹‰à¸² `/forgot-password` à¹€à¸žà¸·à¹ˆà¸­à¸‚à¸­à¸¥à¸´à¸‡à¸à¹Œà¸£à¸µà¹€à¸‹à¹‡à¸•à¸£à¸«à¸±à¸ªà¸œà¹ˆà¸²à¸™ | ðŸ”¹ **Journey 2** (Flow 2.3 Reset Password) | â€¢ à¸à¸¹à¹‰à¸„à¸·à¸™à¸šà¸±à¸à¸Šà¸µà¸œà¸¹à¹‰à¹ƒà¸Šà¹‰ |
| **`cy.updateProfileName()`** | `name: string` | à¹€à¸‚à¹‰à¸²à¸«à¸™à¹‰à¸² `/profile/edit` à¹€à¸›à¸¥à¸µà¹ˆà¸¢à¸™à¸Šà¸·à¹ˆà¸­à¹à¸¥à¸°à¸à¸”à¸šà¸±à¸™à¸—à¸¶à¸ | ðŸ”¹ **Journey 2** (Flow 2.4 Profile) | â€¢ à¸à¸²à¸£à¹€à¸›à¸¥à¸µà¹ˆà¸¢à¸™à¸Šà¸·à¹ˆà¸­à¸œà¸¹à¹‰à¹ƒà¸Šà¹‰à¸‡à¸²à¸™ |
| **`cy.updateProfileContact()`** | `{ phone, line, facebook }` | à¹€à¸‚à¹‰à¸²à¸«à¸™à¹‰à¸² `/profile/edit/contact` à¸­à¸±à¸›à¹€à¸”à¸•à¸Šà¹ˆà¸­à¸‡à¸—à¸²à¸‡à¸•à¸´à¸”à¸•à¹ˆà¸­à¹à¸¥à¸°à¸à¸”à¸šà¸±à¸™à¸—à¸¶à¸ | ðŸ”¹ **Journey 2** (Flow 2.4 Contact) | â€¢ à¸‚à¹‰à¸­à¸¡à¸¹à¸¥à¸•à¸´à¸”à¸•à¹ˆà¸­à¸ªà¸³à¸«à¸£à¸±à¸šà¸œà¸¹à¹‰à¸‹à¸·à¹‰à¸­/à¸œà¸¹à¹‰à¸‚à¸²à¸¢ |
| **`cy.sellerFastForwardOrder()`** | `orderId: string`, `status?: string` | **API Fast-Forward:** à¸ªà¹ˆà¸‡à¸„à¸³à¸‚à¸­ HTTP à¸•à¸£à¸‡à¹„à¸›à¸¢à¸±à¸‡ Backend à¸”à¹‰à¸§à¸¢ JWT à¸‚à¸­à¸‡à¸œà¸¹à¹‰à¸‚à¸²à¸¢à¹€à¸žà¸·à¹ˆà¸­à¹€à¸›à¸¥à¸µà¹ˆà¸¢à¸™à¸ªà¸–à¸²à¸™à¸° Order à¸—à¸±à¸™à¸—à¸µ (50ms) | ðŸ”¹ **Journey 4** (Order Lifecycle) | â€¢ à¸œà¸¹à¹‰à¸‚à¸²à¸¢à¸à¸£à¸­à¸à¹€à¸¥à¸‚à¸žà¸±à¸ªà¸”à¸¸ (Tracking Number Flow)<br>â€¢ à¸œà¸¹à¹‰à¸‚à¸²à¸¢à¸›à¸à¸´à¹€à¸ªà¸˜/à¸¢à¸à¹€à¸¥à¸´à¸à¸„à¸³à¸ªà¸±à¹ˆà¸‡à¸‹à¸·à¹‰à¸­ (Seller Cancellation)<br>â€¢ à¸•à¸£à¸§à¸ˆà¸ªà¸­à¸šà¸à¸²à¸£à¸«à¸±à¸à¸„à¹ˆà¸²à¸˜à¸£à¸£à¸¡à¹€à¸™à¸µà¸¢à¸¡ Tier à¸œà¸¹à¹‰à¸‚à¸²à¸¢ |
| **`cy.cleanTestOrder()`** | `orderId: string \| number` | **Data Teardown:** à¸¥à¹‰à¸²à¸‡à¸‚à¹‰à¸­à¸¡à¸¹à¸¥ Order à¹à¸¥à¸° Order Items à¸—à¸µà¹ˆà¸ªà¸£à¹‰à¸²à¸‡à¸‚à¸¶à¹‰à¸™à¸­à¸­à¸à¸ˆà¸²à¸ Local PostgreSQL à¹€à¸žà¸·à¹ˆà¸­à¹„à¸¡à¹ˆà¹ƒà¸«à¹‰à¸¡à¸µà¸‚à¸¢à¸°à¸ªà¸°à¸ªà¸¡ | ðŸ”¹ **Journey 4** (Order Lifecycle) | â€¢ à¸—à¸¸à¸ Test Journey à¸—à¸µà¹ˆà¸¡à¸µà¸à¸²à¸£à¸ªà¸£à¹‰à¸²à¸‡à¸„à¸³à¸ªà¸±à¹ˆà¸‡à¸‹à¸·à¹‰à¸­à¸ˆà¸£à¸´à¸‡ (Checkout & Payment)<br>â€¢ à¸à¸²à¸£à¸—à¸”à¸ªà¸­à¸š Concurrency à¸ªà¸±à¹ˆà¸‡à¸‹à¸·à¹‰à¸­à¸à¸²à¸£à¹Œà¸”à¸žà¸£à¹‰à¸­à¸¡à¸à¸±à¸™ |
| **`cleanupLocalDbOrder`** *(Cypress Node Task)* | `orderId: string \| number` | à¸£à¸±à¸™à¸„à¸³à¸ªà¸±à¹ˆà¸‡ SQL `DELETE` à¸œà¹ˆà¸²à¸™ `docker exec tcg_postgres psql` à¹€à¸žà¸·à¹ˆà¸­à¸¥à¸šà¸£à¸°à¹€à¸šà¸µà¸¢à¸™à¹ƒà¸™à¸à¸²à¸™à¸‚à¹‰à¸­à¸¡à¸¹à¸¥ Local | ðŸ”¹ **Journey 4** (à¹€à¸£à¸µà¸¢à¸à¸œà¹ˆà¸²à¸™ `cy.cleanTestOrder`) | â€¢ à¹ƒà¸Šà¹‰à¸„à¸¹à¹ˆà¸à¸±à¸š `cy.cleanTestOrder` à¹ƒà¸™à¸£à¸°à¸”à¸±à¸šà¸£à¸°à¸šà¸šà¸›à¸à¸´à¸šà¸±à¸•à¸´à¸à¸²à¸£ |

---

## ðŸ” 2. à¹€à¸ˆà¸²à¸°à¸¥à¸¶à¸à¸£à¸²à¸¢à¸¥à¸°à¹€à¸­à¸µà¸¢à¸”à¸„à¸³à¸ªà¸±à¹ˆà¸‡à¹à¸¥à¸°à¸à¸²à¸£à¹€à¸Šà¸·à¹ˆà¸­à¸¡à¹‚à¸¢à¸‡à¹ƒà¸™à¹à¸•à¹ˆà¸¥à¸° Journey

```mermaid
graph TD
    subgraph CypressCommands ["ðŸ› ï¸ Cypress Custom Commands & Tasks"]
        cmdLogin["ðŸ”‘ cy.loginByMock(role)"]
        cmdFastForward["âš¡ cy.sellerFastForwardOrder(orderId, status)"]
        cmdClean["ðŸ§¹ cy.cleanTestOrder(orderId)"]
        taskClean["ðŸ˜ Task: cleanupLocalDbOrder"]
    end

    subgraph UserJourneys ["ðŸŒ² Core User Journeys"]
        J1["🧭 Journey 1: Navigation & Discovery<br>(US-02: Browse & Search Cards)"]
        J2["🔒 Journey 2: Auth, Security & Profile<br>(US-08-1: Edit User Info)"]
        J3["🛒 Journey 3: Cart & Checkout<br>(US-03: Shopping Cart System)"]
        J4["📦 Journey 4: Order Lifecycle & Stock<br>(US-10-5: Stock Reservation)"]
        J_Future["🔮 Future Journeys:<br>• US-05-3: Update-Product (Seller)<br>• Admin Approval"]
    end

    cmdLogin -->|à¸ˆà¸³à¸¥à¸­à¸‡ Buyer Session| J3
    cmdLogin -->|à¸ˆà¸³à¸¥à¸­à¸‡ Buyer Session| J4
    cmdLogin -.->|à¸ˆà¸³à¸¥à¸­à¸‡ Seller/Admin Session| J_Future

    cmdFastForward -->|à¸¢à¸´à¸‡ API à¸ªà¹ˆà¸‡à¸‚à¸­à¸‡à¹à¸—à¸™à¸ªà¸¥à¸±à¸š UI| J4
    cmdFastForward -.->|à¸¢à¸´à¸‡ API à¸›à¸£à¸±à¸šà¸ªà¸–à¸²à¸™à¸°à¸„à¸³à¸ªà¸±à¹ˆà¸‡à¸‹à¸·à¹‰à¸­| J_Future

    cmdClean -->|Hook afterEach à¸¥à¸š Order| J4
    cmdClean --> taskClean
    taskClean -->|à¸¥à¸šà¸‚à¹‰à¸­à¸¡à¸¹à¸¥à¹ƒà¸™ Local DB| J4

    style cmdLogin fill:#1a2332,stroke:#4a85e0,stroke-width:2px,color:#fff
    style cmdFastForward fill:#2a1e12,stroke:#ef8225,stroke-width:2px,color:#fff
    style cmdClean fill:#19281e,stroke:#3bb273,stroke-width:2px,color:#fff
    style taskClean fill:#19281e,stroke:#3bb273,stroke-width:2px,color:#fff
```

---

### ðŸ”¹ 2.1 `cy.loginByMock(role)`
* **Journey à¸—à¸µà¹ˆà¹ƒà¸Šà¹‰:** 
  * [`03_cart_and_checkout_flow.cy.ts`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/e2e/03_cart_and_checkout_flow.cy.ts)
  * [`04_order_fastforward_lifecycle.cy.ts`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/e2e/04_order_fastforward_lifecycle.cy.ts)
* **à¸—à¸³à¹„à¸¡ Journey 1 à¹à¸¥à¸° 2 à¸–à¸¶à¸‡à¹„à¸¡à¹ˆà¹ƒà¸Šà¹‰?**
  * **Journey 1 (Navigation):** à¸¡à¸¸à¹ˆà¸‡à¹€à¸™à¹‰à¸™à¸—à¸”à¸ªà¸­à¸š Guest à¸—à¸µà¹ˆà¸¢à¸±à¸‡à¹„à¸¡à¹ˆà¹„à¸”à¹‰à¹€à¸‚à¹‰à¸²à¸ªà¸¹à¹ˆà¸£à¸°à¸šà¸š
  * **Journey 2 (Auth):** à¸¡à¸¸à¹ˆà¸‡à¹€à¸™à¹‰à¸™à¸—à¸”à¸ªà¸­à¸šà¸•à¸±à¸§à¹à¸šà¸šà¸Ÿà¸­à¸£à¹Œà¸¡à¸¥à¹‡à¸­à¸à¸­à¸´à¸™à¸ˆà¸£à¸´à¸‡ (`/login`) à¹à¸¥à¸°à¸à¸²à¸£à¸—à¸³à¸‡à¸²à¸™à¸‚à¸­à¸‡ `ProtectedRoute` à¹€à¸¡à¸·à¹ˆà¸­à¹„à¸¡à¹ˆà¸¡à¸µ Token à¸ˆà¸¶à¸‡à¸•à¹‰à¸­à¸‡à¸›à¸¥à¹ˆà¸­à¸¢ Storage à¹ƒà¸«à¹‰à¸§à¹ˆà¸²à¸‡à¹€à¸›à¸¥à¹ˆà¸²
* **à¸£à¸¹à¸›à¹à¸šà¸šà¸à¸²à¸£à¸™à¸³à¹„à¸›à¹ƒà¸Šà¹‰à¹ƒà¸™à¹‚à¸„à¹‰à¸”:**
  ```typescript
  beforeEach(() => {
    // à¸à¸³à¸«à¸™à¸” Session à¸œà¸¹à¹‰à¸‹à¸·à¹‰à¸­à¸à¹ˆà¸­à¸™à¹€à¸‚à¹‰à¸²à¸«à¸™à¹‰à¸² Cart
    cy.loginByMock('CUSTOMER');
    cy.visit('/cart');
  });
  ```

---

### ðŸ”¹ 2.2 `cy.sellerFastForwardOrder(orderId, status)`
* **Journey à¸—à¸µà¹ˆà¹ƒà¸Šà¹‰:**
  * [`04_order_fastforward_lifecycle.cy.ts`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/e2e/04_order_fastforward_lifecycle.cy.ts)
* **à¹€à¸«à¸•à¸¸à¸œà¸¥à¸—à¸²à¸‡à¸ªà¸–à¸²à¸›à¸±à¸•à¸¢à¸à¸£à¸£à¸¡:**
  * à¸à¸²à¸£à¸ªà¸¥à¸±à¸šà¸œà¸¹à¹‰à¹ƒà¸Šà¹‰à¸šà¸™à¹€à¸šà¸£à¸²à¸§à¹Œà¹€à¸‹à¸­à¸£à¹Œà¹„à¸›à¸¡à¸² (Buyer âž” Seller âž” Buyer) à¸¡à¸µ Overhead à¸”à¹‰à¸²à¸™à¹€à¸§à¸¥à¸²à¸ªà¸¹à¸‡à¸¡à¸²à¸ (~30-40 à¸§à¸´à¸™à¸²à¸—à¸µ)
  * à¸„à¸³à¸ªà¸±à¹ˆà¸‡à¸™à¸µà¹‰à¹€à¸›à¸¥à¸µà¹ˆà¸¢à¸™à¸à¸£à¸°à¸šà¸§à¸™à¸à¸²à¸£à¸à¸±à¹ˆà¸‡à¸œà¸¹à¹‰à¸‚à¸²à¸¢à¹ƒà¸«à¹‰à¸à¸¥à¸²à¸¢à¹€à¸›à¹‡à¸™à¸„à¸³à¸‚à¸­ **HTTP Request à¸•à¸£à¸‡à¹„à¸›à¸—à¸µà¹ˆ API Backend** à¹‚à¸”à¸¢à¹ƒà¸Šà¹‰à¹€à¸§à¸¥à¸²à¹€à¸žà¸µà¸¢à¸‡ ~50ms à¸—à¸³à¹ƒà¸«à¹‰à¹€à¸—à¸ªà¸£à¸±à¸™à¹„à¸”à¹‰à¹€à¸£à¹‡à¸§à¹à¸¥à¸°à¹€à¸ªà¸–à¸µà¸¢à¸£ à¹„à¸¡à¹ˆà¸«à¸¥à¸¸à¸” Flaky
* **à¸£à¸¹à¸›à¹à¸šà¸šà¸à¸²à¸£à¸™à¸³à¹„à¸›à¹ƒà¸Šà¹‰à¹ƒà¸™à¹‚à¸„à¹‰à¸”:**
  ```typescript
  // à¸”à¸±à¸à¸ˆà¸±à¸š orderId à¸ˆà¸²à¸ URL à¹à¸¥à¹‰à¸§à¸ªà¸±à¹ˆà¸‡à¹ƒà¸«à¹‰à¸œà¸¹à¹‰à¸‚à¸²à¸¢à¸¢à¸·à¸™à¸¢à¸±à¸™à¸à¸²à¸£à¸ªà¹ˆà¸‡à¸‚à¸­à¸‡à¸—à¸±à¸™à¸—à¸µ
  cy.sellerFastForwardOrder(createdOrderId, 'FULFILLED');
  ```

---

### ðŸ”¹ 2.3 `cy.cleanTestOrder(orderId)` & `cleanupLocalDbOrder` (Node Task)
* **Journey à¸—à¸µà¹ˆà¹ƒà¸Šà¹‰:**
  * [`04_order_fastforward_lifecycle.cy.ts`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/e2e/04_order_fastforward_lifecycle.cy.ts)
* **à¹€à¸«à¸•à¸¸à¸œà¸¥à¸—à¸²à¸‡à¸ªà¸–à¸²à¸›à¸±à¸•à¸¢à¸à¸£à¸£à¸¡:**
  * à¸—à¸¸à¸à¸„à¸£à¸±à¹‰à¸‡à¸—à¸µà¹ˆà¸—à¸”à¸ªà¸­à¸šà¸à¸²à¸£à¸ªà¸±à¹ˆà¸‡à¸‹à¸·à¹‰à¸­ à¸‚à¹‰à¸­à¸¡à¸¹à¸¥à¸ˆà¸°à¸–à¸¹à¸à¸šà¸±à¸™à¸—à¸¶à¸à¸¥à¸‡à¹ƒà¸™à¸•à¸²à¸£à¸²à¸‡ `orders` à¹à¸¥à¸° `order_items` à¸‚à¸­à¸‡ Local PostgreSQL à¸ˆà¸£à¸´à¸‡
  * à¸«à¸²à¸à¹„à¸¡à¹ˆà¸¥à¸šà¸­à¸­à¸ à¸‚à¹‰à¸­à¸¡à¸¹à¸¥à¸ˆà¸°à¸ªà¸°à¸ªà¸¡à¸ˆà¸™à¹€à¸•à¹‡à¸¡ à¹à¸¥à¸°à¸ªà¸•à¹‡à¸­à¸à¸‚à¸­à¸‡à¸à¸²à¸£à¹Œà¸”à¸ˆà¸°à¸¥à¸”à¸¥à¸‡à¹€à¸£à¸·à¹ˆà¸­à¸¢à¹† à¸ˆà¸™à¹€à¸à¸´à¸”à¸‚à¹‰à¸­à¸œà¸´à¸”à¸žà¸¥à¸²à¸” "Out of Stock" à¹ƒà¸™à¸£à¸­à¸šà¸–à¸±à¸”à¹„à¸›
  * à¸„à¸³à¸ªà¸±à¹ˆà¸‡à¸™à¸µà¹‰à¸ˆà¸°à¸—à¸³à¸‡à¸²à¸™à¹ƒà¸™ Hook `afterEach()` à¹€à¸žà¸·à¹ˆà¸­à¸£à¸±à¸šà¸›à¸£à¸°à¸à¸±à¸™à¸§à¹ˆà¸²à¹„à¸¡à¹ˆà¸§à¹ˆà¸²à¸ˆà¸°à¹€à¸—à¸ªà¸œà¹ˆà¸²à¸™à¸«à¸£à¸·à¸­à¹€à¸—à¸ªà¸•à¸ à¸‚à¹‰à¸­à¸¡à¸¹à¸¥à¸—à¸”à¸ªà¸­à¸šà¸ˆà¸°à¸–à¸¹à¸à¸¥à¹‰à¸²à¸‡à¸—à¸´à¹‰à¸‡ 100%
* **à¸£à¸¹à¸›à¹à¸šà¸šà¸à¸²à¸£à¸™à¸³à¹„à¸›à¹ƒà¸Šà¹‰à¹ƒà¸™à¹‚à¸„à¹‰à¸”:**
  ```typescript
  afterEach(() => {
    if (createdOrderId) {
      cy.cleanTestOrder(createdOrderId);
    }
  });
  ```

---

## ðŸ”® 3. à¹à¸™à¸§à¸—à¸²à¸‡à¸à¸²à¸£à¹€à¸žà¸´à¹ˆà¸¡ Custom Commands à¸ªà¸³à¸«à¸£à¸±à¸š Use Cases à¹ƒà¸«à¸¡à¹ˆ

à¹€à¸¡à¸·à¹ˆà¸­à¸¡à¸µ Use Case à¹ƒà¸™ Sprint 4 à¹€à¸žà¸´à¹ˆà¸¡à¹€à¸•à¸´à¸¡ à¹ƒà¸«à¹‰à¸›à¸à¸´à¸šà¸±à¸•à¸´à¸•à¸²à¸¡à¸¡à¸²à¸•à¸£à¸à¸²à¸™à¹‚à¸„à¸£à¸‡à¸ªà¸£à¹‰à¸²à¸‡à¸™à¸µà¹‰:

1. **à¹€à¸žà¸´à¹ˆà¸¡ Implementation à¹ƒà¸™ [`commands.ts`](file:///c:/Devops_NEW/Code_devops/tcg-store-frontend/cypress/support/commands.ts):**
   ```typescript
   Cypress.Commands.add('sellerAddTrackingNumber', (orderId: string, trackingNo: string) => {
     // à¸¢à¸´à¸‡ API à¸«à¸£à¸·à¸­à¸à¸£à¸­à¸à¸Ÿà¸­à¸£à¹Œà¸¡à¹€à¸¥à¸‚à¸žà¸±à¸ªà¸”à¸¸
   });
   ```
2. **à¸›à¸£à¸°à¸à¸²à¸¨ TypeScript Interface Declaration:**
   ```typescript
   declare global {
     namespace Cypress {
       interface Chainable {
         sellerAddTrackingNumber(orderId: string, trackingNo: string): Chainable<void>;
       }
     }
   }
   ```
3. **à¸­à¸±à¸›à¹€à¸”à¸•à¸¥à¸‡à¹ƒà¸™à¸•à¸²à¸£à¸²à¸‡ Commands Matrix à¹ƒà¸™à¹€à¸­à¸à¸ªà¸²à¸£à¸™à¸µà¹‰à¹€à¸ªà¸¡à¸­**

### ðŸ”¹ 2.4 `cy.loginAsAdmin()`
* **Journey à¸—à¸µà¹ˆà¹ƒà¸Šà¹‰:**
  * Journey 5 (Admin Platform)
* **à¹€à¸«à¸•à¸¸à¸œà¸¥à¸—à¸²à¸‡à¸ªà¸–à¸²à¸›à¸±à¸•à¸¢à¸à¸£à¸£à¸¡:**
  * à¹ƒà¸Šà¹‰à¸ªà¸³à¸«à¸£à¸±à¸š Login à¹€à¸‚à¹‰à¸²à¸£à¸°à¸šà¸šà¸œà¹ˆà¸²à¸™ UI à¸”à¹‰à¸§à¸¢à¸ªà¸´à¸—à¸˜à¸´à¹Œ ADMIN à¹€à¸žà¸·à¹ˆà¸­à¸—à¸”à¸ªà¸­à¸šà¸Ÿà¸µà¹€à¸ˆà¸­à¸£à¹Œà¸‚à¸­à¸‡ Platform Admin (US-11-1, US-11-2, US-11-3)

