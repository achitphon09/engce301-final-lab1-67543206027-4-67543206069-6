# 📊 Log Dashboard & Task Board - Final Lab (Set 1)

## 👤 จัดทำโดย
1. **นายอชิตพล โอชารส** รหัสนักศึกษา 67543206027-4
2. **นายเมธิวัฒน์ ชมชื่น** รหัสนักศึกษา 67543206069-6

---

## 🏗️ System Architecture
```text
           +-----------------------+
           |       INTERNET        |
           |   (HTTP:80/HTTPS:443) |
           +-----------+-----------+
                       |
                       v
           +-----------------------+
           |     Nginx Gateway     |
           |  (Reverse Proxy/SSL)  |
           +-----------+-----------+
                       |
     +-----------------+-----------------+-----------------+
     |                 |                 |                 |
     v                 v                 v                 v
+-----------+    +-----------+    +-----------+    +-----------+
|  Frontend |    |   Auth    |    |   Task    |    |    Log    |
|  (Static) |    |  Service  |    |  Service  |    |  Service  |
|           |    |  (:3001)  |    |  (:3002)  |    |  (:3003)  |
+-----------+    +-----+-----+    +-----+-----+    +-----+-----+
                       |                 |                 |
                       +--------+--------+-----------------+
                                |
                                v
                       +-----------------+
                       |   PostgreSQL    |
                       |    (Shared)     |
                       +-----------------+
```

---

## 🚀 เทคโนโลยีที่ใช้ (Tech Stack)
- **Frontend**: HTML5, Vanilla JavaScript, CSS (Static)
- **Backend**: Node.js, Express.js (Microservices)
- **Database**: PostgreSQL 15
- **Reverse Proxy**: Nginx (API Gateway, SSL/TLS, Rate Limiting)
- **Infrastructure**: Docker, Docker Compose

---

## 🛠️ วิธีการติดตั้งและรันโปรเจกต์ (Setup Guide)

1. **คัดลอกไฟล์ Environment**:
   ```bash
   cp .env.example .env
   ```

2. **สร้างใบรับรอง SSL (Self-signed)**:
   สร้างไฟล์ `cert.pem` และ `key.pem` ไว้ในโฟลเดอร์ `nginx/certs/`

3. **รันระบบด้วย Docker Compose**:
   ```bash
   docker-compose up -d --build
   ```

4. **เข้าใช้งาน**:
   - หน้าเว็บหลัก (HTTPS): `https://localhost`
   - ระบบจะ Redirect จาก `http://localhost` ไปยัง `https://localhost` โดยอัตโนมัติ

---

## 🔒 API Endpoints
- **Auth Service**: `/api/auth/` (Login, Register, Me)
- **Task Service**: `/api/tasks/` (CRUD Tasks - ต้องการ JWT)
- **Log Service**: `/api/logs/` (View System Logs - ต้องการ Admin Role)

---

## 🛡️ ฟีเจอร์ความปลอดภัย (Security Features)
- **Rate Limiting**: จำกัดการ Login (5 requests/min) และ API ทั่วไป (30 requests/min) โดย Nginx
- **Security Headers**: HSTS, X-Frame-Options, X-Content-Type-Options
- **JWT Authentication**: ตรวจสอบสิทธิ์การเข้าถึง API ทุกส่วน
- **Role-based Access Control (RBAC)**: แยกสิทธิ์ระหว่าง `member` และ `admin` (เช่น การดู Log)