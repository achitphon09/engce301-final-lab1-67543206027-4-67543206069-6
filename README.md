# 📊 Log Dashboard & Task Board - Final Lab (Set 1)

## 👤 จัดทำโดย
1. **นายอชิตพล โอชารส** รหัสนักศึกษา 67543206027-4
2. **นายเมธิวัฒน์ ชมชื่น** รหัสนักศึกษา 67543206069-6

---

## Screenshots
1. [01 - Docker Running](screenshots/01_docker_running.png)
2. [02 - HTTPS in Browser](screenshots/02_https_browser.png)
3. [03 - Login Success](screenshots/03_login_success.png)
4. [04 - Login Fail](screenshots/04_login_fail.png)
5. [05 - Create Task](screenshots/05_create_task.png)
6. [06 - Get Tasks](screenshots/06_get_tasks.png)
7. [07 - Update Task](screenshots/07_update_task.png)
8. [08 - Delete Task](screenshots/08_delete_task.png)
9. [09 - No JWT 401 Unauthorized](screenshots/09_no_jwt_401.png)
10. [10 - API Logs](screenshots/10_logs_api.png)
11. [11 - Rate Limiting](screenshots/11_rate_limit.png)
12. [12 - Frontend Screenshot](screenshots/12_frontend_screenshot.png)

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

### 🔒 HTTPS Implementation
ในระบบนี้ HTTPS ถูกจัดการที่ **Nginx Gateway**:
1. **SSL Termination**: Nginx รับการเชื่อมต่อผ่านพอร์ต 443 โดยใช้ใบรับรอง SSL (Cert/Key) ที่อยู่ใน `nginx/certs/`
2. **Encrypted Traffic**: ข้อมูลที่ส่งระหว่าง Client และ Nginx จะถูกเข้ารหัสทั้งหมด
3. **Internal Proxy**: หลังจากถอดรหัสแล้ว Nginx จะส่งข้อมูลต่อไปยัง Microservices ต่างๆ ภายในวงเครือข่าย Docker (Internal Network) ทำให้ส่วนขยายของระบบยังคงปลอดภัย
4. **HTTP Redirect**: มีการ Redirect อัตโนมัติจากพอร์ต 80 (HTTP) ไปยัง 443 (HTTPS) เพื่อบังคับความปลอดภัย

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

## 👥 Seed Users (ข้อมูลผู้ใช้งานเริ่มต้น)
| Username | Email | Password | Role |
| :--- | :--- | :--- | :--- |
| `alice` | `alice@lab.local` | `alice123` | `member` |
| `bob` | `bob@lab.local` | `bob456` | `member` |
| `admin` | `admin@lab.local` | `adminpass` | `admin` |

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