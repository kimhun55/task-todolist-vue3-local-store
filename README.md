# แอปพลิเคชันรายการสิ่งที่ต้องทำ (Vue.js To-Do List)

แอปพลิเคชันรายการสิ่งที่ต้องทำที่พัฒนาด้วย Vue.js 3 และ Tailwind CSS พร้อมรองรับภาษาไทยอย่างเต็มรูปแบบ

## คุณสมบัติเด่น

- **จัดการงาน**: สร้าง, อ่าน, แก้ไข, และลบงาน (CRUD)
- **รายละเอียดงาน**: ใส่หัวข้อ, รายละเอียด, รูปภาพ, วันที่เริ่ม/สิ้นสุด, และแท็ก
- **สถานะงาน**: รอการทำงาน -> กำลังทำ -> เสร็จแล้ว
- **มุมมอง**: สลับระหว่างมุมมองการ์ด (Grid) และมุมมองรายการ (List)
- **ตัวกรอง**: กรองงานตามสถานะ
- **ระบบยืนยันตัวตน**: ล็อกอินเข้าใช้งาน (ใช้ .env)
- **รองรับทุกหน้าจอ**: ใช้งานได้ดีทั้งบนมือถือ, แท็บเล็ต, และคอมพิวเตอร์
- **ภาษาไทย**: เมนูและข้อความทั้งหมดเป็นภาษาไทย

## ตัวอย่างหน้าจอ

### หน้าเข้าสู่ระบบ (Login)
![หน้าเข้าสู่ระบบ](/Users/kimhun55/.gemini/antigravity/brain/c241e224-37a4-49d9-8628-193602d031a2/readme_login_screen_1764554216651.png)

### หน้าหลัก (Main App)
![หน้าหลัก](/Users/kimhun55/.gemini/antigravity/brain/c241e224-37a4-49d9-8628-193602d031a2/readme_main_app_screen_1764554231887.png)

## สิ่งที่ต้องมี (Prerequisites)

- Node.js (เวอร์ชัน 16.0.0 ขึ้นไป)
- npm (เวอร์ชัน 7.0.0 ขึ้นไป)

## การติดตั้ง (Installation)

1.  โคลนโปรเจกต์ (หรือแตกไฟล์):
    ```bash
    git clone <repository-url>
    cd todolist_vuejs
    ```

2.  ติดตั้ง dependencies:
    ```bash
    npm install
    ```

## การตั้งค่า (Configuration)

1.  สร้างไฟล์ `.env` ในโฟลเดอร์หลักของโปรเจกต์ (ถ้ายังไม่มี)
2.  กำหนดชื่อผู้ใช้และรหัสผ่านที่ต้องการ:
    ```env
    VITE_USERNAME=admin
    VITE_PASSWORD=password123
    ```

## การรันโปรแกรม (Running Locally)

เริ่มรันเซิร์ฟเวอร์สำหรับพัฒนา:

```bash
npm run dev
```

เปิดเบราว์เซอร์และไปที่ `http://localhost:5173`

## การสร้างไฟล์สำหรับใช้งานจริง (Building for Production)

สร้างไฟล์สำหรับนำไปใช้งานจริง (Production Build):

```bash
npm run build
```

ไฟล์ที่ได้จะอยู่ในโฟลเดอร์ `dist/`

## การนำไปใช้งาน (Deployment)

### นำไปใช้บน Static Server (เช่น Nginx, Apache)

1.  รันคำสั่ง `npm run build`
2.  อัปโหลดไฟล์ทั้งหมดในโฟลเดอร์ `dist/` ไปยัง public directory ของเว็บเซิร์ฟเวอร์
3.  ตรวจสอบให้แน่ใจว่าเซิร์ฟเวอร์รองรับ Single Page Applications (SPA) โดยการ redirect request ทั้งหมดไปที่ `index.html`

### นำไปใช้บน Vercel (ตัวอย่าง)

1.  ติดตั้ง Vercel CLI: `npm i -g vercel`
2.  รันคำสั่ง `vercel` ในโฟลเดอร์โปรเจกต์
3.  ทำตามขั้นตอนที่ปรากฏบนหน้าจอ

### นำไปใช้บน Netlify (ตัวอย่าง)

1.  ลากโฟลเดอร์ `dist/` ไปวางในหน้า Dashboard ของ Netlify Drop
2.  หรือเชื่อมต่อกับ Git repository และตั้งค่า build command เป็น `npm run build` และ publish directory เป็น `dist`

## โครงสร้างโปรเจกต์ (Project Structure)

- `src/App.vue`: คอมโพเนนต์หลักของแอปพลิเคชัน
- `src/components/`:
    - `Login.vue`: หน้าเข้าสู่ระบบ
    - `TaskForm.vue`: ป๊อปอัพสำหรับสร้าง/แก้ไขงาน
    - `TaskList.vue`: แสดงรายการงานในรูปแบบการ์ดหรือตาราง
    - `TaskItem.vue`: การ์ดแสดงงานแต่ละชิ้น
    - `TaskDetail.vue`: ป๊อปอัพแสดงรายละเอียดงาน
- `src/style.css`: ไฟล์ตั้งค่า Tailwind CSS
