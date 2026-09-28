# 🚀 CI/CD & DevOps Documentation Portal

โฟลเดอร์นี้รวบรวมเอกสารคู่มือและรายงานทางเทคนิคสำหรับกระบวนการ **CI/CD Pipeline, Automated Quality Gates, และ DevOps Infrastructure** ของแพลตฟอร์ม Mythix TCG โดยแบ่งออกเป็น 3 หมวดหมู่หลักตามโครงสร้างการทำงาน:

---

## ⏱️ ลำดับและจังหวะการรันการทดสอบในวงจรพัฒนา (When Tests Are Executed)

ก่อนที่จะลงลึกในแต่ละขั้นตอนของสายพาน CI/CD สิ่งสำคัญคือการเข้าใจ **จังหวะและสภาพแวดล้อมในการรันการทดสอบแต่ละประเภท** (โดยเฉพาะความแตกต่างระหว่าง **Unit Test** ที่เน้นความเร็วระดับวินาที กับ **End-to-End (E2E) Test** ที่จำลองพฤติกรรมผู้ใช้จริงทั้งระบบ):

```mermaid
flowchart TD
    subgraph Phase1 ["💻 1. Local Machine (เครื่องนักพัฒนา)"]
        Dev["👨‍💻 เขียนโค้ด / ฟีเจอร์เสร็จสิ้น"]
        LocalUT["⚡ Vitest / JUnit (รันอัตโนมัติ 5-10 วินาที)"]
        LocalE2E["🌲 Cypress GUI: npm run test:e2e:open<br>(เปิดดูการคลิกสดๆ / ใช้เดโมตรวจงาน)"]
        Dev --> LocalUT --> LocalE2E
    end

    subgraph Phase2 ["⚙️ 2. CI Quality Gate (ตอนเปิด Pull Request)"]
        PR["🔀 เปิด Pull Request เข้าสู่ 'main' หรือ 'develop'"]
        CI_Fast["1️⃣ Fast Gates: Lint, Unit Test, Security Audit"]
        CI_Spin["2️⃣ Spin-up Full Stack Docker (DB + BE + FE)"]
        CI_E2E["3️⃣ Cypress Headless E2E Execution"]
        Gate{"E2E ผ่าน 100%<br>หรือไม่?"}
        AllowMerge["🟢 ผ่าน: อนุญาตให้กด Merge โค้ดได้"]
        BlockMerge["🛑 ไม่ผ่าน: บล็อกการ Merge ทันที (Quality Gate)"]

        PR --> CI_Fast --> CI_Spin --> CI_E2E --> Gate
        Gate -- "ผ่าน" --> AllowMerge
        Gate -- "ไม่ผ่าน" --> BlockMerge
    end

    subgraph Phase3 ["🚀 3. Staging Deployment (หลัง Deploy ระบบทดสอบ)"]
        StagingDeploy["📦 Deploy สู่ Staging Server (เช่น ต่อ Neon Cloud DB)"]
        SmokeE2E["🎯 Smoke E2E Tests: เช็กเส้นทางวิกฤต (Login, Search, Buy)"]
        StagingDeploy --> SmokeE2E
    end

    subgraph Phase4 ["🌙 4. Nightly Build (รันอัตโนมัติข้ามคืน)"]
        Cron["⏰ ทุกเที่ยงคืน (Scheduled Cron Job)"]
        FullRegression["🧪 Full Regression Test: รันทุก Scenario ทุก Journey 100%"]
        Cron --> FullRegression
    end

    Phase1 --> Phase2 --> Phase3
```

### 💡 สรุป 4 จังหวะการรันการทดสอบ (Testing Triggers & Environments):
1. **💻 บนเครื่อง Local (ตอนพัฒนาเสร็จ & ก่อนส่งงาน):**
   * นักพัฒนารัน Unit Test เพื่อความเร็ว และใช้ `npm run test:e2e:open` เพื่อดูการจำลองหน้าจอและคลิกจริง เหมาะสำหรับการตรวจงานและเดโมสดให้อาจารย์ดู
2. **⚙️ ใน CI Pipeline (ตอนเปิด Pull Request สู่ Branch หลัก):**
   * ทำหน้าที่เป็น **Quality Gate ขั้นเด็ดขาด** โดยระบบ CI (GitHub Actions / Jenkins) จะบูต Docker Container ขึ้นมาครบชุด แล้วรัน Cypress Headless หากมี Test Case ใดพัง ระบบจะล็อกปุ่ม Merge ทันที ป้องกันโค้ดมีบั๊กหลุดเข้า `main`
3. **🚀 บน Staging Server (หลัง Deploy ก่อนขึ้น Production):**
   * รันเฉพาะ **Smoke Test (Critical Journeys)** เช่น ทดสอบการต่อฐานข้อมูลจริงบน Cloud, การชำระเงิน และการจัดส่ง เพื่อยืนยันว่าคอนฟิกูเรชันบน Production Environment สมบูรณ์พร้อมเปิดให้ผู้ใช้จริง
4. **🌙 Nightly Build (รันตอนเที่ยงคืน):**
   * เมื่อโปรเจกต์มี E2E Test ครอบคลุมหลายสิบ Use Case การรันทุกเคสใน PR จะทำให้รอนาน ระบบจึงตั้งเวลาตอนเที่ยงคืนเพื่อรัน Regression Test ชุดใหญ่ครบ 100% ข้ามคืนโดยไม่รบกวนเวลาทำงานของทีม

---

## 🔄 ภาพรวมกระบวนการ: จาก Unit Test สู่ Production (Flow Overview)

แผนภาพแสดงความสัมพันธ์ระหว่าง **Developer**, **ด่านตรวจคุณภาพ (Quality Gates)** และ **สายพานการส่งมอบอัตโนมัติ (CI/CD Pipeline)** ตั้งแต่การส่งโค้ดจนกระทั่งขึ้นสู่ระบบจริง:

```mermaid
flowchart LR
    Dev["👨‍💻 Developer<br>Push Code / PR"] --> CI["🚀 CI Pipeline (สายพานอัตโนมัติ)"]
    
    subgraph PipelineStages ["ขั้นตอนการตรวจสอบใน CI (Quality Gates)"]
        Lint["1. Linting & Typecheck<br>(Oxlint / tsc)"]
        UT["2. รัน Unit & Component Test<br>(Vitest + RTL / JUnit 5)"]
        Audit["3. Security & Dependency Audit<br>(npm audit --audit-level=high)"]
        Build["4. Docker Multi-Stage Build<br>(Nginx / JRE Alpine)"]
    end
    
    CI --> Lint --> UT
    UT -->|✅ ผ่าน 100%| Audit --> Build --> Prod["🌐 ขึ้น Production สำเร็จ<br>(Production Ready)"]
    UT -.->|❌ เทสไม่ผ่าน| Stop["🛑 สั่งหยุดสายพานทันที (Build Failed)<br>บล็อกไม่ให้โค้ดมีบั๊กหลุดขึ้นระบบ"]
    Audit -.->|⚠️ เจอช่องโหว่ร้ายแรง| Stop
```

### 💡 คำอธิบายขั้นตอนในสายพาน (Pipeline Stages Breakdown):
1. **👨‍💻 Push Code / PR:** เมื่อนักพัฒนาส่งโค้ดใหม่เข้ามายัง Repository ระบบ Webhook จะปลุกระบบ CI (Jenkins หรือ GitHub Actions) ขึ้นมาทำงานโดยอัตโนมัติ
2. **1. Linting & Typecheck:** ตรวจสอบความถูกต้องของไวยากรณ์ (Syntax) และ Type Safety ด้วย TypeScript และ Oxlint
3. **2. รัน Unit & Component Test (ด่านตรวจหัวใจสำคัญ):**
   * ระบบสั่งรันชุดทดสอบอัตโนมัติทั้ง Frontend (24 ไฟล์ 71 เคส) และ Backend Service Tests
   * **กรณีเทสไม่ผ่าน:** ระบบจะตัดการทำงาน (Abort) และแจ้งเตือนทันที โค้ดจะไม่ถูกส่งต่อไปยังขั้นตอนถัดไป
4. **3. Security & Dependency Audit:** ตรวจสอบช่องโหว่ความปลอดภัยของ Third-Party Libraries ด้วย `npm audit` และสแกนหา Secret หลุด
5. **4. Docker Multi-Stage Build:** ประกอบร่าง Production Image ขนาดเล็กกระทัดรัด (Alpine Linux) พร้อมตรวจสอบ `docker compose config`
6. **🌐 ขึ้น Production สำเร็จ:** เมื่อผ่านครบทุกด่านอย่างสมบูรณ์ โค้ดจึงจะได้รับอนุญาตให้ Deploy ขึ้นสู่เซิร์ฟเวอร์จริงได้อย่างมั่นใจ 100%

---

## 📂 โครงสร้างโฟลเดอร์ (Directory Hierarchy)

```
docs/CI_CD/
├── 🧪 Unitest/
│   ├── frontend_unit_and_component_tests.md    # คู่มือและรายงาน Vitest + React Testing Library (71 Tests Passed)
│   └── sprint1_backend_unit_tests.md           # คู่มือและรายงาน JUnit 5 + Mockito Service Layer Tests
│
├── 🛡️ Security Audit/
│   └── security_and_dependency_audit_guide.md  # คู่มือ npm audit, Dependabot, Trivy & Security Gates
│
├── 🐳 DockerBuild/
│   └── docker_build_and_containerization_guide.md # คู่มือ Multi-stage Dockerfile & Docker Compose Full-Stack
│
├── 🌲 E2E/
│   ├── README.md                               # แคตตาล็อกและสารบัญชุดทดสอบ E2E ทั้งหมด
│   ├── cypress_e2e_testing_guide.md            # คู่มือการติดตั้งและสถาปัตยกรรม Cypress v16.1
│   ├── custom_commands_mapping.md              # แผนผังความสัมพันธ์ Custom Commands กับ Journeys
│   ├── 01_Navigation_and_Discovery/            # Journey 1: ค้นหาการ์ด & เมนูหลัก
│   ├── 02_Authentication_and_Security/         # Journey 2: ระบบ Login & Route Protection
│   ├── 03_Cart_and_Inventory/                  # Journey 3: ตะกร้าสินค้า & สต็อก
│   └── 04_Order_Fulfillment_Lifecycle/         # Journey 4: สั่งซื้อ ➔ ส่งของ (API Fast-Forward) ➔ Teardown
│
├── ci_cd_pipelines_overview.md                 # ภาพรวมสถาปัตยกรรม CI/CD ทั้งระบบ (GitHub Actions + Jenkins)
├── jenkins_cicd_comprehensive_guide.md         # คู่มือ Jenkins Orchestration & Declarative Pipelines
└── ci_cd_quality_gates_report.md               # รายงานการตรวจรับงาน Quality Gates
```

---

## 📑 สรุปรายละเอียดแต่ละหมวดหมู่

### 1. 🧪 `/Unitest`
* [Frontend Unit & Component Testing Guide](./Unitest/frontend_unit_and_component_tests.md): อธิบายระบบการทดสอบฝั่ง Frontend ด้วย Vitest v4.1 และ React Testing Library ครบทั้ง 25 ไฟล์ 73 เคส (เจาะลึก 5 Pillars of Testing Infrastructure)
* [Backend Unit Testing Guide](./Unitest/sprint1_backend_unit_tests.md): อธิบายระบบการทดสอบฝั่ง Backend Service Layer ด้วย JUnit 5 และ Mockito

### 2. 🛡️ `/Security Audit`
* [Security & Dependency Audit Guide](./Security%20Audit/security_and_dependency_audit_guide.md): อธิบายกลยุทธ์ป้องกันช่องโหว่ซัพพลายเชน 4 เลเยอร์ (npm audit `--audit-level=high`, Dependabot weekly PRs, Gitleaks, และ Trivy Scanner)

### 3. 🐳 `/DockerBuild`
* [Docker Build & Containerization Guide](./DockerBuild/docker_build_and_containerization_guide.md): อธิบายสถาปัตยกรรม Multi-Stage Build ของ Frontend (Nginx Alpine) และ Backend (Eclipse Temurin JRE 21 Non-root) พร้อมการตรวจสอบผ่าน `docker compose config`

### 4. 🌲 `/E2E`
* [E2E Test Suite Catalog](./E2E/README.md): สารบัญชุดทดสอบ E2E และคู่มือรันแยกแต่ละ Journey
* [Cypress E2E Testing Guide](./E2E/cypress_e2e_testing_guide.md): คู่มือการทดสอบจำลองผู้ใช้จริงด้วย Cypress v16.1 พร้อมคำสั่งเปิด GUI และ Headless Mode
* [Custom Commands Matrix](./E2E/custom_commands_mapping.md): แผนผังความสัมพันธ์ Custom Commands กับ Core User Journeys
* [Journey 1: Navigation & Discovery](./E2E/01_Navigation_and_Discovery/README.md)
* [Journey 2: Authentication & Security](./E2E/02_Authentication_and_Security/README.md)
* [Journey 3: Cart & Inventory](./E2E/03_Cart_and_Inventory/README.md)
* [Journey 4: Order Fulfillment Lifecycle](./E2E/04_Order_Fulfillment_Lifecycle/README.md) (พร้อมเทคนิค API Fast-Forward และ Local DB Teardown)

---

## 🌐 เอกสารภาพรวมในระดับ Root CI_CD
* [CI/CD Pipelines Overview](./ci_cd_pipelines_overview.md)
* [Jenkins CI/CD Comprehensive Guide](./jenkins_cicd_comprehensive_guide.md)
* [CI/CD Quality Gates Report](./ci_cd_quality_gates_report.md)
