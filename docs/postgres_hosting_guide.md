# 🐘 คู่มือการ Self-Host PostgreSQL (Local Dev → K8s)

สรุปวิธี host PostgreSQL เอง ไม่พึ่ง Supabase หรือ Cloud DB ใด ๆ ตาม scope ที่ห้ามใช้ External API/Service (ดู `tcg_project_guide.md`)

---

## 1. ทำไมไม่ใช้ Supabase

Supabase self-hosted = Postgres + Auth (GoTrue) + Storage + Realtime + Kong Gateway หลาย container รวมกัน ซ้ำซ้อนกับ Spring Security ที่ทำ Auth เองอยู่แล้ว และผิด scope ถ้าใช้ Supabase Cloud (ต้องพึ่งอินเทอร์เน็ต/API ภายนอก)

ใช้ Postgres image เปล่า (`postgres:16`) พอ — เบา, ควบคุมเองได้เต็มที่, ตรงกับ requirement Docker/K8s ของวิชาอยู่แล้ว

---

## 2. Local Dev — Docker Compose

`docker-compose.yml` (root ของ repo):

```yaml
services:
  db:
    image: postgres:16
    restart: unless-stopped
    environment:
      POSTGRES_DB: tcgstore
      POSTGRES_USER: app
      POSTGRES_PASSWORD: ${DB_PASSWORD:-devpassword}
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app -d tcgstore"]
      interval: 5s
      timeout: 5s
      retries: 5

  backend:
    build: ./backend
    depends_on:
      db:
        condition: service_healthy
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/tcgstore
      SPRING_DATASOURCE_USERNAME: app
      SPRING_DATASOURCE_PASSWORD: ${DB_PASSWORD:-devpassword}
    ports:
      - "8080:8080"

volumes:
  pgdata:
```

รัน:
```bash
docker compose up -d
```

Backend (`application.yml` หรือ `application.properties`) ใช้ env var เหล่านี้ ไม่ hardcode credential ในโค้ด:
```yaml
spring:
  datasource:
    url: ${SPRING_DATASOURCE_URL}
    username: ${SPRING_DATASOURCE_USERNAME}
    password: ${SPRING_DATASOURCE_PASSWORD}
```

> [!TIP]
> `pgdata` เป็น named volume — data ไม่หายแม้ `docker compose down` (หายเฉพาะถ้าสั่ง `docker compose down -v`)

---

## 3. Production — Kubernetes (Minikube / GCP)

### 3.1 Secret เก็บ credential

`k8s/postgres-secret.yml`:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: postgres-secret
type: Opaque
stringData:
  POSTGRES_DB: tcgstore
  POSTGRES_USER: app
  POSTGRES_PASSWORD: changeme-in-real-deploy
```

### 3.2 PersistentVolumeClaim (กัน data หายเวลา pod restart)

`k8s/postgres-pvc.yml`:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

### 3.3 Deployment

`k8s/postgres-deployment.yml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
        - name: postgres
          image: postgres:16
          ports:
            - containerPort: 5432
          envFrom:
            - secretRef:
                name: postgres-secret
          volumeMounts:
            - name: postgres-storage
              mountPath: /var/lib/postgresql/data
      volumes:
        - name: postgres-storage
          persistentVolumeClaim:
            claimName: postgres-pvc
```

> [!WARNING]
> `replicas: 1` เท่านั้น — Postgres แบบ single PVC ไม่รองรับ multi-replica (data corruption) ถ้าอยากได้ HA ต้องใช้ StatefulSet + replication ซึ่งเกินขอบเขตวิชานี้

### 3.4 Service (internal DNS ให้ Backend เรียก)

`k8s/postgres-service.yml`:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: postgres-service
spec:
  selector:
    app: postgres
  ports:
    - port: 5432
      targetPort: 5432
```

### 3.5 Backend ต่อยังไง

Backend Deployment ตั้ง env:
```yaml
SPRING_DATASOURCE_URL: jdbc:postgresql://postgres-service:5432/tcgstore
```
`postgres-service` คือ internal DNS name ของ K8s — ไม่ต้อง expose ออก internet, ไม่ต้องพึ่ง Cloud DB ใด ๆ

Apply ทั้งหมด:
```bash
kubectl apply -f k8s/postgres-secret.yml
kubectl apply -f k8s/postgres-pvc.yml
kubectl apply -f k8s/postgres-deployment.yml
kubectl apply -f k8s/postgres-service.yml
```

---

## 4. Checklist

- [ ] `docker-compose.yml` มี service `db` (postgres:16) + volume ถาวร
- [ ] Backend อ่าน DB credential จาก env var ไม่ hardcode
- [ ] `.env` / secret ไม่ commit เข้า Git (ใส่ `.gitignore`)
- [ ] K8s: Secret + PVC + Deployment (replicas:1) + Service ครบ 4 manifest
- [ ] ทดสอบ `docker compose up` แล้ว backend connect DB ได้จริงก่อน push CP4
- [ ] ทดสอบ `kubectl apply` บน Minikube แล้ว backend resolve `postgres-service` ได้ก่อน CP5

---

## 5. สรุป

Self-host Postgres ผ่าน Docker Compose (dev) และ K8s Deployment+PVC+Secret+Service (prod) ครบ scope "ห้าม External API/Service" ไม่ต้องพึ่ง Supabase หรือ Cloud DB ใด ๆ ทั้งสิ้น
