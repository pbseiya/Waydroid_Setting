# คู่มือการติดตั้งและตั้งค่า Waydroid แบบสมบูรณ์

คู่มือนี้รวบรวมขั้นตอนตั้งแต่การติดตั้ง Waydroid, การตั้งค่าตัวเปิด (Waywes), การเปิดใช้งาน Google Play Store, การตั้งค่าเสียง และการเข้าถึงไฟล์ในเครื่อง

## 1. การติดตั้ง Waydroid และ Waywes

### 1.1 ติดตั้ง Waydroid และ Weston
จำเป็นต้องติดตั้ง Weston ด้วยเพราะ script Waywes ต้องใช้ในการเปิดหน้าต่าง
```bash
# อัปเดตระบบ
sudo apt update
sudo apt upgrade

# ติดตั้ง Waydroid และ Weston
sudo apt install waydroid weston
```

### 1.2 ติดตั้ง Waywes (ตัวช่วยเปิด Waydroid)
Waywes เป็นสคริปต์ช่วยเปิด Waydroid ในโหมดต่างๆ (Windowed/Fullscreen) ผ่าน Weston
```bash
# ไปที่โฟลเดอร์ home
cd ~

# ดึงไฟล์จาก Github (ถ้ายังไม่มี)
git clone https://github.com/KSMaan45/waywes.git

# เข้าโฟลเดอร์และกำหนดสิทธิ์
cd ~/waywes
chmod +x waywes.sh

# การใช้งาน
./waywes.sh
# เลือกโหมดที่ต้องการจากเมนู

### ฟีเจอร์เพิ่มเติม (Extras)
ในเมนูข้อ 9. Extras จะมีคำสั่งช่วยอำนวยความสะดวก:
1.  **Add Script To Path:** เพิ่ม waywes ลงใน Path ทำให้เรียกใช้คำสั่ง `waywes.sh` ได้จากทุกที่ (ต้องใช้ root/sudo)
2.  **Remove Script From Path:** ลบออกจาก Path

### การแก้ไขความละเอียดหน้าจอ (Custom Resolution)
หากต้องการความละเอียดอื่นนอกเหนือจากที่มีให้ สามารถแก้ไขไฟล์ `waywes.sh` ได้โดยตรง
*   เปิดไฟล์ด้วย Text Editor
*   แก้ไขค่า `--width` และ `--height` ตามต้องการ (เช่น `--width 2560 --height 1440`)
*   เปลี่ยนชื่อเมนูให้สอดคล้องกัน (เช่นแก้ `"1920x1080 Fullscreen")` เป็น `"2560x1440 Fullscreen")`)
```

---

## 2. การเปิดใช้งาน Google Play Store
Waydroid ปกติจะไม่มี Play Store ต้องติดตั้งแบบ GAPPS

### 2.1 ติดตั้ง Image แบบ GAPPS
```bash
# หยุด Waydroid ก่อน
waydroid session stop

# ติดตั้งใหม่ (ข้อมูลเก่าจะหาย)
sudo waydroid init -s GAPPS -f
```

### 2.2 ลงทะเบียนอุปกรณ์ (Play Protect Certification)
จำเป็นต้องทำเพื่อให้ใช้งาน Play Store ได้
1.  **เปิด Waydroid:** รัน `./waywes.sh` แล้วเลือกโหมดสักอัน
2.  **เอา Android ID:** เปิด Terminal ใหม่แล้วรันคำสั่ง:
    ```bash
    sudo waydroid shell sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select value from main where name = 'android_id';"
    ```
3.  **ลงทะเบียน:** เอาตัวเลขที่ได้ไปใส่ที่ [https://www.google.com/android/uncertified](https://www.google.com/android/uncertified)
4.  **รอ:** ประมาณ 10-20 นาที
5.  **รีสตาร์ท:** ปิด Waydroid (`waydroid session stop`) แล้วเปิดใหม่
6.  **แก้ปัญหา Login ไม่ได้:** หากยังติด Authentication required ให้ลอง **Sign out แล้ว Sign in ใหม่** ใน Play Store หรือรันคำสั่งล้างค่า:
    ```bash
    sudo waydroid shell pm clear com.google.android.gsf
    sudo waydroid shell pm clear com.google.android.gms
    ```

---

## 3. การให้สิทธิ์เข้าถึง Storage (Host Files)
เพื่อให้ Waydroid มองเห็นไฟล์ในเครื่อง Linux ของเรา

### วิธีที่ 1: Mount โฟลเดอร์ Home (แนะนำ - ง่ายและปลอดภัย)
ใช้คำสั่ง `mount --bind` เพื่อเชื่อมโฟลเดอร์จากเครื่องเราเข้าไปใน Waydroid

**ขั้นตอนการทำ:**
1.  **สร้างโฟลเดอร์ปลายทางใน Waydroid:** (ทำครั้งเดียว)
    ```bash
    # สร้างโฟลเดอร์ชื่อ HostData ไว้ใน Download ของ Android
    sudo waydroid shell mkdir -p /sdcard/Download/HostData
    ```
2.  **สั่ง Mount โฟลเดอร์:** (ทำทุกครั้งหลังเปิดเครื่อง หรือใส่ใน script)
    สมมติว่าคุณอยากแชร์โฟลเดอร์ `~/Documents` ของคุณ
    ```bash
    # คำสั่ง mount (เปลี่ยน ~/Documents เป็นโฟลเดอร์ที่คุณต้องการ)
    sudo mount --bind ~/Documents ~/.local/share/waydroid/data/media/0/Download/HostData
    ```
3.  **ตรวจสอบ:**
    เปิดแอปดูไฟล์ใน Android ไปที่ `Internal Storage > Download > HostData` จะเจอไฟล์ Documents ของคุณทันที

**หมายเหตุ:**
*   วิธีนี้เป็นการเข้าถึงไฟล์แบบ Real-time แก้ไขไฟล์ไหน ไฟล์นั้นเปลี่ยนทันทีทั้งสองฝั่ง
*   หากรีสตาร์ทเครื่อง Linux สิทธิ์การ Mount จะหายไป ต้องรันคำสั่งข้อ 2 ใหม่อีกครั้ง

### วิธีที่ 2: ใช้คำสั่งบังคับให้สิทธิ์แอป (Terminal)
หากแอปมองไม่เห็นไฟล์ หรือไม่ขอสิทธิ์ สามารถใช้คำสั่งบังคับให้สิทธิ์ได้

**1. หาชื่อ Package ของแอป:**
```bash
waydroid app list
```
*มองหาชื่อเช่น `com.facebook.katana` หรือ `org.telegram.messenger`*

**2. สั่งให้สิทธิ์ (Grant Permission):**
สมมติชื่อแอปคือ `com.example.app` ให้รันคำสั่ง:
```bash
waydroid shell pm grant com.example.app android.permission.READ_EXTERNAL_STORAGE
waydroid shell pm grant com.example.app android.permission.WRITE_EXTERNAL_STORAGE
```

### ข้อควรระวังเรื่อง "พื้นที่จัดเก็บ"
**สำคัญ:** แอปใน Waydroid จะมองไม่เห็นไฟล์ใน `/home/user/` ของเราโดยตรง เพราะทำงานใน Container แยก
*   ตำแหน่งไฟล์ของ Waydroid บนเครื่องเราอยู่ที่:
    `~/.local/share/waydroid/data/media/0/`
*   หากต้องการเอาภาพไปใส่ใน Gallery ของ Waydroid ให้ก๊อปปี้ไปวางที่:
    `~/.local/share/waydroid/data/media/0/Pictures`

---

## 4. การปรับเสียงให้ดังขึ้น (Sound & Youtube)
หากเสียงเบาผิดปกติ ให้ตรวจสอบดังนี้

### 4.1 ปรับใน Android Settings
1.  ใน Waydroid เลื่อนแถบด้านบนลงมา
2.  หาตัวปรับเสียง (Volume) แล้วเร่งให้สุด
3.  เข้า Settings > Sound > เพิ่ม Media Volume

### 4.2 ปรับจากเครื่อง Linux (PulseAudio/PipeWire)
บางครั้งเสียงใน Android เร่งสุดแล้ว แต่เสียง Output ของแอป Waydroid บน Linux ถูกลดไว้
1.  ติดตั้งโปรแกรมจัดการเสียง (ถ้ายังไม่มี):
    ```bash
    sudo apt install pavucontrol
    ```
2.  เปิดโปรแกรม `PulseAudio Volume Control` (pavucontrol)
3.  ไปที่แท็บ **Playback**
4.  หาแถบของ **Waydroid** (หรืออาจชื่อว่า `ALSA plug-in [writer]`)
5.  **เร่งเสียงให้เกิน 100%** (เช่น 120-130%) จะช่วยให้เสียงดังขึ้นชัดเจน

### 4.3 สั่งปรับเสียงผ่าน Terminal (ใน Waydroid ไม่มีปุ่ม)
เนื่องจากในหน้าจอ Waydroid อาจไม่มีปุ่ม Volume ให้กด คุณสามารถใช้คำสั่ง Terminal จำลองการกดปุ่มได้

**วิธีที่ 1: สั่งกดปุ่ม "เร่งเสียง" แบบรัวๆ (แนะนำ - ง่ายที่สุด)**
ใช้คำสั่งจำลองการกดปุ่ม Volume Up รัวๆ 15 ครั้งเพื่อให้เสียงดังสุด
```bash
for i in {1..15}; do sudo waydroid shell input keyevent 24; done
```
*(ถ้าจะลดเสียง ให้เปลี่ยนเลข `24` เป็น `25`)*

**วิธีที่ 2: สั่งระบุระดับเสียง (Advance)**
คำสั่งนี้จะกำหนดค่าความดังเป็นตัวเลข 0-15 ได้โดยตรง
`waydroid shell cmd media_session volume --show --stream 3 --set <ระดับเสียง 0-15>`
*   **ดังสุด (15):** `waydroid shell cmd media_session volume --show --stream 3 --set 15`
*   **เงียบ (0):** `waydroid shell cmd media_session volume --show --stream 3 --set 0`

---

## สรุปคำสั่งสำคัญที่ใช้บ่อย
*   **เปิด Waydroid:** `cd ~/waywes && ./waywes.sh`
*   **ปิด Waydroid:** `waydroid session stop`
*   **ดู Android ID:** `sudo waydroid shell sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select value from main where name = 'android_id';"`
*   **เข้า Shell:** `sudo waydroid shell`
