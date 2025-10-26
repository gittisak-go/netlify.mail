# netlify.mail

netlify.mail เป็นโปรเจ็กต์ตัวอย่างสำหรับรับและส่งอีเมลผ่านฟังก์ชันบน Netlify (หรือแอปที่เกี่ยวข้อง) — README นี้เป็นเทมเพลตแนะนำการติดตั้ง การพัฒนา การตั้งค่า และตัวอย่างการใช้งาน ให้แก้ไขค่าต่าง ๆ ตามความต้องการของโปรเจ็กต์จริง

## สรุปโปรเจ็กต์
โปรเจ็กต์นี้มีจุดประสงค์เพื่อ:
- จัดการการส่ง/รับอีเมลแบบง่ายผ่าน Netlify Functions (หรือ API ของผู้ให้บริการอีเมล)
- เป็นตัวอย่างการตั้งค่า environment variables และการ deploy ขึ้น Netlify
- เป็นฐานเริ่มต้นสำหรับรวมบริการอีเมล เช่น SMTP, SendGrid, Mailgun ฯลฯ

> หมายเหตุ: README นี้เป็นเทมเพลต — ปรับข้อความ, ตัวอย่างคำสั่ง, และตัวแปรแวดล้อมให้ตรงกับโค้ดจริงใน repo

## คุณสมบัติ (Features)
- รับข้อมูลจากฟอร์ม (เช่น contact form) แล้วส่งเป็นอีเมลไปยังผู้รับ
- รองรับการทำงานในเครื่องด้วย `netlify dev` (ถ้าใช้ Netlify CLI)
- ตัวอย่างการตั้งค่า provider อีเมล (SMTP / API-based)

## โครงสร้างโฟลเดอร์ (ตัวอย่าง)
- netlify/functions/       — ฟังก์ชันของ Netlify (serverless)
- src/                    — โค้ดฝั่ง client (ถ้ามี)
- README.md               — ไฟล์นี้
- netlify.toml            — การตั้งค่า Netlify (ถ้ามี)
- package.json            — สคริปต์และ dependency

ปรับโครงสร้างตามโปรเจ็กต์จริงของคุณ

## เริ่มต้น (Prerequisites)
- Node.js (แนะนำเวอร์ชัน LTS)
- npm หรือ yarn
- (ถ้าใช้) Netlify CLI: `npm install -g netlify-cli`
- บัญชีผู้ให้บริการอีเมล เช่น SendGrid, Mailgun หรือ SMTP credentials

## การติดตั้ง (Local)
1. โคลน repository
   ```
   git clone https://github.com/gittisak-go/netlify.mail.git
   cd netlify.mail
   ```

2. ติดตั้ง dependency
   ```
   npm install
   # หรือ
   yarn install
   ```

3. ตั้งค่า environment variables
   สร้างไฟล์ `.env` หรือใช้ Secrets ของ Netlify ตามตัวอย่างด้านล่าง

   ตัวอย่าง `.env` (อย่าเก็บ .env ใน git)
   ```
   # ตัวอย่างสำหรับ SMTP
   SMTP_HOST=smtp.example.com
   SMTP_PORT=587
   SMTP_USER=your-smtp-user
   SMTP_PASS=your-smtp-password

   # หรือสำหรับ API เช่น SendGrid
   SENDGRID_API_KEY=SG.xxxxxx

   # ตั้งค่าอีเมลเป้าหมาย
   RECIPIENT_EMAIL=you@example.com

   # ค่าทั่วไป
   NODE_ENV=development
   ```

4. รันในเครื่อง (ถ้าใช้ Netlify Functions)
   ```
   netlify dev
   ```
   หรือ สั่งสคริปต์ที่กำหนดใน package.json:
   ```
   npm run dev
   ```

## ตัวอย่างการเรียกใช้งาน (API)
สมมติว่าเปิดฟังก์ชัน `/.netlify/functions/send-email`:
- ตัวอย่าง POST request (JSON)
  ```
  POST /.netlify/functions/send-email
  Content-Type: application/json

  {
    "from": "visitor@example.com",
    "name": "Visitor Name",
    "subject": "สอบถามข้อมูล",
    "message": "สวัสดีครับ สนใจบริการ..."
  }
  ```

ตอบกลับควรมีสถานะและข้อความยืนยัน เมื่อเกิดข้อผิดพลาดให้คืนรหัสสถานะ (เช่น 400, 500) พร้อมรายละเอียดสั้น ๆ

## การตั้งค่า Netlify (deploy)
1. สร้าง site ใน Netlify และเชื่อมต่อกับ GitHub repo นี้
2. กำหนด Build command และ Publish directory (ถ้ามี)
   - ตัวอย่าง Build command: `npm run build`
   - Publish directory: `dist` หรือ `build` (แล้วแต่โปรเจ็กต์)
3. ตั้งค่า Environment variables ใน Netlify UI (Site settings > Build & deploy > Environment)
   - ใส่ค่า SMTP / API keys / RECIPIENT_EMAIL ตามที่ใช้จริง
4. Push โค้ดขึ้น branch ที่เชื่อมกับ Netlify แล้ว Netlify จะทำการ deploy อัตโนมัติ

ตัวอย่าง `netlify.toml` (พื้นฐาน)
```toml
[build]
  command = "npm run build"
  functions = "netlify/functions"
  publish = "dist"

[context.production.environment]
  NODE_ENV = "production"
```

## การทดสอบ
- ทดสอบ unit / integration (ถ้ามี) ด้วย jest/mocking
- ทดสอบฟังก์ชันส่งอีเมลโดยใช้ sandbox API หรือ SMTP testing server (เช่น Mailtrap) เพื่อหลีกเลี่ยงการส่งอีเมลจริงระหว่างพัฒนา

## การดีบัก (Debugging)
- เปิด log ในฟังก์ชัน (console.log) และตรวจดูใน Netlify function logs
- ตรวจสอบค่าตัวแปรแวดล้อมว่าตั้งถูกต้อง
- ถ้าใช้ third-party API ให้ตรวจสอบ quota / key / permission

## ข้อควรระวังด้านความปลอดภัย
- ห้ามเก็บ API keys / credentials ใน git
- ใช้ Secrets ของ Netlify หรือตัวจัดการความลับอื่น ๆ
- ตรวจสอบและกรอง input จากผู้ใช้ก่อนส่งอีเมล (ป้องกัน header injection, XSS)

## Contributing
ยินดีรับ PR และ issue:
1. Fork repository
2. สร้าง branch ใหม่: `git checkout -b feature/your-feature`
3. ทำการเปลี่ยนแปลงและ commit
4. เปิด PR พร้อมคำอธิบายการเปลี่ยนแปลง

## License
ระบุสัญญาอนุญาต (เช่น MIT) หรือเปลี่ยนเป็นที่ต้องการ:
```
MIT License
```

## ติดต่อ
- เจ้าของ: gittisak-go
- Email: (ระบุ email ถ้าต้องการให้ติดต่อได้)

---

