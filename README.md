# คู่มือการติดตั้งและตั้งค่า Waydroid แบบสมบูรณ์

คู่มือนี้รวบรวมขั้นตอนตั้งแต่การติดตั้ง Waydroid, การตั้งค่าตัวเปิด (Waywes), การเปิดใช้งาน Google Play Store, การตั้งค่าเสียง และการเข้าถึงไฟล์ในเครื่อง

## 1. การติดตั้ง Waydroid และ Waywes

### 1.1 ติดตั้ง Waydroid และ Weston
> [!NOTE]
> โดยปกติ Waydroid จะติดตั้งในโหมด **Phone/Tablet** ให้ทันที (ไม่ใช่ Smart TV) และไม่มีเมนูให้เลือกตอนติดตั้ง

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
4.  *   เปลี่ยนชื่อเมนูให้สอดคล้องกัน (เช่นแก้ `"1920x1080 Fullscreen")` เป็น `"2560x1440 Fullscreen")`)
```

### 1.3 (Advanced) การเปลี่ยนโหมดเป็น Smart TV
หากต้องการให้หน้าตา (UI) ของ Waydroid เป็นแบบ Android TV สามารถทำได้โดยเปลี่ยน property ของระบบ

**เปลี่ยนเป็น Smart TV:**
```bash
# เปลี่ยนค่า
sudo waydroid prop set ro.build.characteristics tv

# รีสตาร์ท Waydroid
waydroid session stop
```

**เปลี่ยนกลับเป็น Phone/Tablet (ค่าเดิม):**
```bash
sudo waydroid prop set ro.build.characteristics default
waydroid session stop
```
*หมายเหตุ: การเปลี่ยนเป็น TV อาจทำให้ Play Store เปลี่ยนเป็นเวอร์ชัน TV และแอปบางตัวที่ไม่รองรับ TV อาจค้นหาไม่เจอ*

---

## 2. การเปิดใช้งาน Google Play Store
Waydroid ปกติจะไม่มี Play Store ต้องติดตั้งแบบ GAPPS


### 2.0 เลือกประเภท ROM (GAPPS vs VANILLA)
ก่อนติดตั้ง ควรเลือก ROM ให้ตรงกับการใช้งาน:
*   **GAPPS (แนะนำ):** มี Google Play Store และ Google Services มาให้พร้อม (เหมาะสำหรับคนทั่วไป)
*   **VANILLA:** เป็น Android เพียวๆ ไม่มี Google Services (เหมาะสำหรับนักพัฒนา หรือคนที่ไม่ต้องการใช้ Google)

**ตรวจสอบว่าเครื่องเราใช้ ROM อะไรอยู่:**
```bash
waydroid prop get waydroid.system_ota
```
*   ถ้าลงท้ายด้วย `GAPPS.json` = ใช้ GAPPS (มี Play Store)
*   ถ้าลงท้ายด้วย `MAINLINE.json` = ใช้ VANILLA (ไม่มี Play Store)

### 2.1 ติดตั้ง Image แบบ GAPPS
```bash
# หยุด Waydroid ก่อน
waydroid session stop

# ติดตั้งใหม่ (ข้อมูลเก่าจะหาย)
sudo waydroid init -s GAPPS -f
```
*ถ้าต้องการ VANILLA ให้เปลี่ยน `-s GAPPS` เป็น `-s VANILLA`*

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

### 2.3 การเปิดใช้งาน ARM Translation (เพื่อให้เห็นแอป LINE/Games)
แอปพลิเคชันมือถือส่วนใหญ่ (เช่น LINE, เกม ROV) ถูกพัฒนามาสำหรับชิป ARM แต่คอมพิวเตอร์ทั่วไปเป็น x86 ทำให้ Play Store มองว่า "ไม่รองรับ" จึงซ่อนแอปไว้
เราต้องติดตั้ง **libhoudini** (สำหรับ Intel) หรือ **libndk** (สำหรับ AMD) เพื่อให้รันแอปเหล่านี้ได้

**ขั้นตอนการติดตั้ง:**
1.  **ติดตั้ง Waydroid Script:**
    ```bash
    cd ~
    git clone https://github.com/casualsnek/waydroid_script
    cd waydroid_script
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    ```
2.  **รัน Script:**
    ```bash
    sudo python3 main.py
    ```
3.  **เลือกเมนู:**
    *   เลือก `Install`
    *   เลือก `libhoudini` (ถ้าใช้ CPU Intel) หรือ `libndk` (ถ้าใช้ CPU AMD)
    *   กด Spacebar เพื่อเลือก แล้วกด Enter
4.  **รอจนเสร็จ** แล้วปิด Waydroid เปิดใหม่

### 2.4 แก้ปัญหาแอปยังหาไม่เจอ (เปลี่ยนชื่อรุ่นมือถือ)
บางครั้ง Play Store จะซ่อนแอปถ้าชื่อรุ่นมือถือดูแปลกๆ (เช่น "WayDroid x86_64 Device") ให้เปลี่ยนชื่อเป็นรุ่นดังๆ เช่น **Pixel 5**
```bash
# เปลี่ยนชื่อรุ่น (ใช้ sudo)
sudo waydroid prop set ro.product.model "Pixel 5"
sudo waydroid prop set ro.product.brand "google"
sudo waydroid prop set ro.product.name "redfin"
sudo waydroid prop set ro.product.device "redfin"
sudo waydroid prop set ro.product.manufacturer "Google"

# ล้างค่า Play Store (สำคัญมาก!)
sudo waydroid shell pm clear com.android.vending
sudo waydroid shell pm clear com.google.android.gms

# รีสตาร์ท Waydroid เพื่อให้ค่าเริ่มทำงาน
waydroid session stop
```
*หลังจากทำเสร็จ ให้ปิด Waydroid แล้วเปิดใหม่ทันที*

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

## 5. การตรวจสอบปัญหาและดู Log
หาก Waydroid เปิดไม่ติด หรือแอปเด้ง สามารถตรวจสอบสาเหตุได้ดังนี้

### 5.1 ดู Log ของระบบ (Waydroid Log)
ดูว่า Container ทำงานปกติหรือไม่ มี Error อะไรตอน Start
*   **ดู Log ย้อนหลัง:** `waydroid log`
*   **ดู Log แบบ Real-time:** `waydroid log -f`
    *(กด Ctrl+C เพื่อออก)*

### 5.2 ดู Log ของ Android (Logcat)
ดูการทำงานภายใน Android เหมือนนักพัฒนา (เช่น ทำไมแอป Crash)
*   **ดูแบบ Real-time:**
    ```bash
    sudo waydroid logcat
    ```
*   **บันทึกลงไฟล์ (เพื่อส่งให้คนอื่นช่วยดู):**
    ```bash
    sudo waydroid logcat > ~/waydroid_log.txt
    ```
    *(ไฟล์จะถูกเซฟไว้ที่ Home folder ชื่อ `waydroid_log.txt`)*

### 5.3 วิธี Start แบบเห็น Log ทันที
หากเปิด Waywes ไม่ติด แนะนำให้ลองเปิดผ่าน Terminal เพื่อดูข้อความ Error:
1.  เปิด Terminal
2.  พิมพ์คำสั่ง: `waydroid session start`
3.  สังเกตข้อความที่ขึ้นมา ถ้ามีสีแดง (Error) ให้นำข้อความนั้นไปค้นหาทางแก้

---

## สรุปคำสั่งสำคัญที่ใช้บ่อย
*   **เปิด Waydroid:** `cd ~/waywes && ./waywes.sh`
*   **ปิด Waydroid:** `waydroid session stop`
*   **ดู Android ID:** `sudo waydroid shell sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select value from main where name = 'android_id';"`
*   **เข้า Shell:** `sudo waydroid shell`
