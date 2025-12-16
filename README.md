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

### วิธีที่ง่ายที่สุด: Mount โฟลเดอร์ Home
โดยปกติ Waydroid จะไม่เห็นไฟล์ใน Home ของเรา ต้องใช้คำสั่ง Mount

```bash
# สร้างโฟลเดอร์ใน Waydroid เพื่อรอรับไฟล์ (ตัวอย่าง: สร้างโฟลเดอร์ HostData ใน Download)
sudo waydroid shell mkdir -p /sdcard/Download/HostData

# ทำการ Mount โฟลเดอร์จากเครื่องเรา (เช่น ~/Documents) ไปที่ Waydroid
# รูปแบบ: sudo mount --bind <โฟลเดอร์เครื่องเรา> <path ของ waydroid data>
# path ของ waydroid data ปกติอยู่ที่ ~/.local/share/waydroid/data/media/0

# ตัวอย่าง: แนะนำให้แก้ไฟล์ config เพื่อให้ mount อัตโนมัติ หรือใช้คำสั่งเฉพาะกิจ
# แต่วิธีที่ง่ายและถาวรกว่าคือการแชร์ผ่าน folder ที่ Waydroid สร้างไว้แล้ว
```

**วิธีแนะนำ (Automatic Mount):**
ปกติ Waydroid จะเข้าถึงโฟลเดอร์ `~/.local/share/waydroid/data/media/0` ได้อยู่แล้ว (ซึ่งคือ `/sdcard` ใน Android)
ดังนั้น **วิธีที่ง่ายที่สุด** คือการก๊อปปี้ไฟล์ที่คุณต้องการใช้ ไปใส่ไว้ใน:
`~/.local/share/waydroid/data/media/0/Download`
แล้วใน Android ก็จะเปิดเจอในถัง Download ทันทีครับ

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
เนื่องจากในหน้าจอ Waydroid อาจไม่มีปุ่ม Volume ให้กด คุณสามารถใช้คำสั่ง Terminal เพื่อสั่งปรับระดับเสียงได้โดยตรง

**รูปแบบคำสั่ง:** `waydroid shell cmd media_session volume --show --stream 3 --set <ระดับเสียง 0-15>`

*   **ปรับเสียงดังสุด (Level 15):**
    ```bash
    waydroid shell cmd media_session volume --show --stream 3 --set 15
    ```
*   **ปรับเสียงปานกลาง (Level 7):**
    ```bash
    waydroid shell cmd media_session volume --show --stream 3 --set 7
    ```
*   **ปิดเสียง (Mute):**
    ```bash
    waydroid shell cmd media_session volume --show --stream 3 --set 0
    ```
*(หมายเหตุ: `--stream 3` คือเสียงเพลง/Media)*

---

## สรุปคำสั่งสำคัญที่ใช้บ่อย
*   **เปิด Waydroid:** `cd ~/waywes && ./waywes.sh`
*   **ปิด Waydroid:** `waydroid session stop`
*   **ดู Android ID:** `sudo waydroid shell sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select value from main where name = 'android_id';"`
*   **เข้า Shell:** `sudo waydroid shell`
