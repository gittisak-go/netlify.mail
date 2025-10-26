# netlify.mail
ขอบคุณ resend 

Resend with Python
📁 โครงสร้าง Repository: bluesky-crosspost
bluesky-crosspost/
├── bluesky/
│   └── fetch_posts.py         # ดึงโพสต์ล่าสุดจาก Bluesky
├── utils/
│   └── formatter.py           # จัดรูปแบบข้อความโพสต์
├── database/
│   └── posts.db               # SQLite สำหรับเก็บโพสต์ที่ดึงมาแล้ว
├── config/
│   └── settings.env           # เก็บ App Password และ DID
├── tests/
│   └── test_fetch.py          # ทดสอบการดึงโพสต์
└── README.md                  # คู่มือการใช้งาน


🧪 ตัวอย่างสคริปต์เริ่มต้น: fetch_posts.py

Pythonfrom atproto import Clientimport sqlite3import os# โหลดข้อมูลจาก .envDID = os.getenv("BLUESKY_DID")APP_PASSWORD = os.getenv("BLUESKY_APP_PASSWORD")# เชื่อมต่อ Blueskyclient = Client()client.login(DID, APP_PASSWORD)# ดึงโพสต์ล่าสุดfeed = client.get_author_feed(actor=DID, limit=5)# เชื่อมต่อฐานข้อมูลconn = sqlite3.connect("database/posts.db")cursor = conn.cursor()cursor.execute("CREATE TABLE IF NOT EXISTS posts (uri TEXT PRIMARY KEY, content TEXT)")# ตรวจสอบและบันทึกโพสต์ใหม่for item in feed.feed:    uri = item.post.uri    content = item.post.record.text    cursor.execute("SELECT 1 FROM posts WHERE uri = ?", (uri,))    if not cursor.fetchone():        print(f"New post: {content}")        cursor.execute("INSERT INTO posts (uri, content) VALUES (?, ?)", (uri, content))conn.commit()conn.close()แสดงบรรทัดเพิ่มเติม

🔐 ตัวอย่าง .env สำหรับ settings.env
BLUESKY_DID=did:plc:xxxxxxxxxxxxxxxxxxxx
BLUESKY_APP_PASSWORD=your-app-password-here


✅ ขั้นตอนถัดไป

ทดสอบสคริปต์ fetch_posts.py ว่าดึงโพสต์ได้จริง
เพิ่มระบบจัดการเวลา (เช่น schedule หรือ cron)
สร้าง API หรือ webhook สำหรับเชื่อมกับ n8n
เตรียม workflow ใน n8n ที่เรียกใช้สคริปต์นี้ผ่าน HTTP หรือ Command Node


คุณอยากให้ผมช่วยสร้างไฟล์ .py, .env, และ README.md พร้อมสำหรับดาวน์โหลดเลยไหมครับ? หรืออยากให้ผมช่วยเขียน workflow JSON สำหรับนำเข้าใน n8n ต่อเลย?
ให้คําติชมของคุณเกี่ยวกับ BizChatวางไฟล์ของคุณที่นี่
