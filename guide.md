# คู่มือการใช้งาน CMS-grader

## สารบัญ

- [การติดตั้ง](#การติดตั้ง)
- [การจัดการการแข่งขัน](#การจัดการการแข่งขัน)
- [การจัดการผู้ใช้งาน](#การจัดการผู้ใช้งาน)
- [การจัดการโจทย์](#การจัดการโจทย์)

## การติดตั้ง

### โปรแกรมที่ต้องการ

- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- SSH และ FTP client เช่น [bitvise](https://www.bitvise.com/ssh-client-download) เป็นต้น

### Import VM

1. ดาวน์โหลด Image จาก [link](https://drive.google.com/drive/folders/1HhSi61-2rsKSy5Oz6VBKIoaiUKs9YLE4?usp=sharing)
1. เปิดโปรแกรม VirtalBox
1. เลือก File->Import Appliance...
1. เลือกไฟล์ Image ที่ดาวน์โหลดมา

### การจัดการระบบเครือขายของ VirtualBox

1. เลือก Settings->Network
1. เลือก Attached to: NAT
1. เลือก Advanced->Port Forwarding
    - ตั้งค่าสำหรับ SSH โดยให้ Guest Port = 22
    - ตั้งค่าสำหรับ AdminWeb โดยให้ Guest Port = 8889
    - ตั้งค่าสำหรับ ContestWeb โดยให้ Guest Port = 80

### ทดสอบระบบ
*** ทดลองให้เครื่องอื่น ๆ ในเครือข่ายเดียวกันลองเข้ามาดู เพราะว่าอาจจะติดปัญหาที่ firewall หรืออื่น ๆ ได้ตอนนำไปใช้งาน

#### ทดลอง SSH

1. เปิด bitvise
1. ในแถบ login หัวข้อ Server ให้กรอก Host, Port
1. ในแถบ login หัวข้อ Authentication ให้กรอก Username เป็น ubuntu
1. กรอก password = "abc123"
1. เปลี่ยน password โดยใช้คำสั่ง passwd
    >passwd

#### ทดลองเข้าสู่ AdminWeb

1. เข้าสู่ AdminWeb ด้วย Host และ Port ที่ตั้งค่าไว้
1. เข้าสู่ระบบด้วย admin:abc123
1. เปลี่ยน password โดยคลิกที่ชื่อด้านซ้ายบน (admin)

#### ทดลองเข้าสู่ ContestWeb

1. เข้าสู่ ContestWeb ด้วย Host และ Port ที่ตั้งค่าไว้

## การจัดการการแข่งขัน

1. เข้าไปที่ AdminWeb
1. สร้าง contest โดยเลือก Contests->(create new contest...)
1. การตั้งค่า contest โดยจะมีอยู่หลายส่วนแต่ในที่นี้จะแนะนำเฉพาะส่วนที่จำเป็น
    - Allowed programming languages คือ ภาษาที่สามารถส่งได้
    - Times: ในที่นี้คือเวลาเปิดและปิดการแข่งขันเป็น UTC
    - ตัวอย่างสามารถดูใน Contests->sandbox
1. contest_id สามารถดูได้จาก URL โดยตัวอย่างต่อไปนี้จะแสดง contest_id = 1
    ```
    http://localhost:8889/contest/1
    ```

## การจัดการผู้ใช้งาน

### การเพิ่ม user

การเพิ่ม user สามารถทำได้ 2 แบบคือ การเพิ่มจาก AdminWeb และการเพิ่มผ่าน command-line

#### การเพิ่ม user จาก AdminWeb (เหมาะสำหรับเพิ่มทีละคน)

1. เข้าไปที่ AdminWeb
1. เลือก Users->(create new user...)
1. กรอกข้อมูล แนะนำให้กรอก First name, Last name, Username และ Password นอกนั้นจะกรอกหรือไม่ก็ได้
1. เพิ่ม user เข้าไปยัง contest โดยเลือก your_contest->Users
1. เลือก user และกด Add user

หมายเหตุ: วิธีนี้สามารถเพิ่ม user ได้ทีละคนเท่านั้น

#### การเพิ่ม user จาก command-line (ไม่แนะนำ)

1. SSH เข้าไปยัง VM
1. ใช้คำสั่ง cmsAddUser เพื่อเพิ่ม user ใหม่
    >cmsAddUser -p "abc123" "นายชนก" "ปฐมเพทาย" "chanok"
1. ใช้คำสั่ง cmsAddParticipation เพื่อเพิ่ม user ไปยัง contest โดยต้องระบุ contest_id ด้วย
    >cmsAddParticipation -c "1" "chanok"

หมายเหตุ:
- วิธีการนี้สามารถเพิ่ม user ได้ทีละคน แต่ว่าสามารถเขียนโปรแกรมได้

#### การเพิ่ม user จาก command-line ด้วย script ที่เตรียมไว้ (แนะนำ)

1. สร้างไฟล์ csv โดยมีตัวอย่างดังนี้
    ```csv
    name,user,password
    นายชนก ปฐมเพทาย,chanok,abc123
    ผศ.ดร.สิรักข์ แก้วจำนงค์,sirak,321cba
    ```

1. upload ไฟล์นี้ไปยัง /home/ubuntu/scripts
1. SSH เข้าไปยัง VM
1. ไปยัง directory ของ script
    >cd /home/ubuntu/scripts
1. เพิ่ม user โดยใช้ script addUsers.py
    >python addUsers.py
1. ตอบที่อยู่ของ csv เช่น
    ```
    CSV Path: users.csv

    # or

    CSV Path: /home/ubuntu/scripts/users.csv
    ```
1. เพิ่ม user ไปยัง contest โดยใช้ script addParticipation.py
    >python addParticipation.py
1. ตอบที่อยู่ของ csv และ contest_id เช่น
    ```
    CSV Path: users.csv
    Contest ID: 1 # อันนี้คือ sandbox ที่มีมาตั้งแต่แรก
    ```

## การจัดการโจทย์

### การเพิ่มโจทย์

1. เข้าไปที่ AdminWeb
1. เลือก Task->(create new task...)
1. การตั้งค่า task โดยจะมีอยู่หลายส่วนแต่ในที่นี้จะแนะนำเฉพาะส่วนที่จำเป็น
    - Name แนะนำให้เป็น ตัวอักษรภาษาอังกฤษ, ตัวเลข และ "_"
    - Title เป็นข้อความอะไรก็ได้
    - Statements คือไฟล์ pdf ที่เราต้อง upload ขึ้นไป
    - Dataset: Default (Live)
        - Time limit คือเวลาที่จำกัดไว้เช่นไม่เกิน 1 วินาที
        - Memory limit คือ memory ที่จำกัดไว้เช่นไม่เกิน 2 MiB

## ขอขอบคุณ

- [https://github.com/ioi-2023/cms](https://github.com/ioi-2023/cms)

## ผู้เขียน

- นายชนก ปฐมเพทาย (cpathompatai@gmail.com)
