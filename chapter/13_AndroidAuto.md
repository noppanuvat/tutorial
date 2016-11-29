# Unofficial Android Auto for MZD Connect (Technical Review)

Unofficial Android Auto ต่อไปเรียกย่อว่า headunit เป็น opensource software ปัจจุบันซอสโค้ดบันทึกที่ https://github.com/gartnera/headunit
มีนักพัฒนาร่วมพัฒนาอยู่จำนวนหนึ่งปัจจุบันพัฒนาจากจุดเริ่มที่ @spadival พัฒนาไว้จน เริ่มทำงานได้ใกล้เคียง Official Android Auto ถึงแม้ปัจจุบันยังไม่อาจโทรศัพท์ได้ขณะใช้ Android Auto แต่มีแนวโน้มปรับปรุงให้ดีขึ้น

ในบทความนี้อ้างถึง headunit เวอร์ชั่น @gartnera มีซฮฟต์แวร์เกี่ยวข้อง 2ตัวที่ติดตั้งในรถ `headunit` และ `input_filter`
- `headunit` เป็น AA ซอฟต์แวร์หลักทำหน้าที่รับข้อมูลจากแอนดรอยด์แล้วแสดงผลบนหน้าจอ 7" MZD Connect
- `input_filter` เป็นซอฟต์แวร์รับคำสั่งจากปุ่มควบคุม เช่น ปุ่มจากพวงมาลัย

# headunit
[Android Auto](https://www.android.com/auto/) เป็นการทำงานร่วมกันระหว่างแอนดรอยด์บนมือถือ และซอฟต์แวร์บนรถ เพื่อให้เข้าใจตรงกัน แอนดรอยด์บนมือถือมีชื่อว่า Android Auto ซอฟต์แวร์บนรถรเรียกว่า headunit และให้ AA แทนการเรียกรวมกันทั้งสองซอฟต์แวร์

กุญแจสำคัญที่ทำให้ Android Auto ทำงานเกิดจากการพัฒนา `headunit` โดย @mikereidis ได้ศึกษาโพรโทคอลระหว่างแอนดรอยด์และเครื่องเสียงที่รองรับ AA และมีนักพัฒนาร่วมพัฒนาเรื่อยมา ตอนนี้มาไกลจากเป้าหมายพัฒนาให้ android tablet เป็นหน้าจอ Headunit (สำหรับรถที่ไม่มีหน้าจอ) ตอนนี้ได้พัฒนาให้รถที่มีหน้าจอสัมผัสแต่ไม่รองรับ Android Auto มี @spadival เป็นนักพัฒนาหลัก

## การคอมไพล์ `headunit`
### คอมไพล์จากไฟล์ @spadival
#### ติดตั้ง virtualbox
ผมเตรียมไฟล์ VirtualBox ไว้แล้วดาวน์โหลดทุกไฟล์จาก https://mega.nz/#F!oVYTWB5L!BiBrAmwuJH7aXdlP8mTkcQ user:ubuntu , password:ubuntu

```bash
sh extract.sh
tar xf mzd-dev.tar.xz
```
ใช้ Virtualbox เปิดไฟล์ภายใน mzd-dev

```bash
git clone -b rotary_button https://github.com/izacus/headunit.git
```

ใช้คำสั่ง `./ltib -m shell` เพื่อเปลี่ยนคอมไพล์เลอร์สำหรับ Freescale imx6q

```bash
./ltib -m shell
cd headunit/mazda
make
```
ทำให้ได้ไฟล์ `headunit` นำไฟล์ไปใส่ในรถ

###คอมไพล์จากไฟล์ @lmagder
ทำง่ายเพราะได้เตรียม compiler ไว้ให้ ป้อนคำสั่งคอมไพล์ได้เลย

```bash
git clone --recursive https://github.com/lmagder/headunit
cd headunit/mazda
make
```

# input_filter
เป็นซอฟต์แวร์ทำหน้าที่ดักข้อความจากปุ่มต่างๆ ทำงานร่วมกับ หากต้องการควบคุมผ่านปุ่มอื่นที่ไม่ใช่หน้าจอสัมผัส ตอนนี้มีทางเดียว แต่ `input_filter` ทำให้เสี่ยงที่เครื่องหน้าจอค้าง

## หน้าจอค้างเมื่อ Factory Reset
AA เวอร์ชั่น @spadival ไม่เกิดปัญหาเมื่อ Factory Reset เพราะไม่ได้แก้ไขไฟล์ sm.conf แต่ควบคุม AA ผ่านพวงมาลัยไม่ได้ @gartnera เพิ่มคุณสมบัติควบคุมผ่านพวงมาลัยแต่จำเป็นต้องใช้ซอฟต์แวร์ `input_filter` โปรแกรม `input_filter` เป็นโปรแกรมย่อย [mazda-connector](https://github.com/jmgao/mazda-connector) ซึ่ง @gartnera พัฒนาจากเดิมไปมาก จากเดิมเช่นปุ่ม Talk ระบบจะส่งคำสั่งให้ MZD Connect เพื่อประมวลผลคำสั่งผ่านเสียง แต่เมื่อต้องการให้กด Talk แล้วส่งเข้า AA จะต้องขวางการทำงานก่อนที่ชุดคำสั่งส่งถึง MZD Connect นักพัฒนา(@jmgao) เลือกติดตั้ง `input_filter` ใน sm.conf([scary part](https://github.com/jmgao/mazda-connector))เพื่อดักข้อมูลก่อนถูกส่งเข้าระบบ
```xml
<service type="process" name="input_filter" path="/tmp/mnt/data/input_filter" autorun="yes" reset_board="no" retry_count="0" affinity_mask="0x02">
    <dependency type="service" value="stage1"/>
</service>
```

จากโค้ดด้านบนมีบรรทัด `path="/tmp/mnt/data/input_filter"` คุณ [@lmagder](https://gitter.im/gartnera/headunit?at=57cb853b6efec7117ca5c78f) พบปัญหาสำคัญเมื่อ Factory Reset ระบบ MZD Connect จะลบข้อมูลที่เก็บใน /tmp/mnt/data/ ทำให้หลังรีบูท MZD Connect ไม่พบ `input_filter` ส่งผลให้ระบบทำงานต่อไม่ได้ เมื่อลองทำความเข้าใจสาเหตุการเก็บ `input_filter` และ `enable_input_filter` ใน `/tmp/mnt/data/` แทนที่จะเก็บในไดเร็กทอรี่เดียวกับ `headunit` `/tmp/mnt/data_persist/dev` มีสองสาเหตุเพราะขณะนั้นยังไม่มี `headunit` และ @jmgao  พบว่าการขวางการทำงาน MZD Connect มีความเสี่ยงทำให้ระบบเกิด Reboot Loop ซึ่งปี 2014 พบปัญหาแล้วแก้ไขได้ยากเพราะการทำงาน watchdog ทำให้เครื่องรีบูทก่อนส่งคำสั่งแก้ไข เพื่อป้องกันไม่ให้เกิดปัญหาดังกล่าวจึงเขียนโปรแกรมให้ `input_filter`ตรวจสอบการทำงาน โดยใช้วิธีอ่านค่ายตัวเลขจากไฟล์ `enable_input_filter` ใช้ตัวเลข 0 1 2 กำหนดการทำงานดังนี้
- 0 หมายถึง `input_filter` ไม่ทำงาน
- 1 หมายถึง `input_filter` ทำงาน 1ครั้ง แล้วเซตเป็น 0 เมื่อรีบูทครั้งต่อไปทำให้ `input_filter` ไม่ทำงาน
- 2 หมายถึง ให้ทำงานตลอด ดังนั้นเมื่อทดสอบแล้วระบบทำงานได้ ให้เซต `enable_input_filter` เป็น 2

เห็นได้ว่าจำเป็นต้องเขียนอ่านไฟล์ `enable_input_filter` จึงเป็นสาเหตุในการเก็บไฟล์ `enable_input_filter` ไว้ในไดเร็กทอรี่ที่แก้ไขได้ ในที่นี้คือ `/tmp/mnt/data/` ขณะที่ `/tmp/mnt/data_persist/dev` ภาวะปกติเป็น read only

ลำดับต่อมาทำความเข้าใจสาเหตุที่ @gartnera ไม่ย้าย `input_filter` แล้วคง `enable_input_filter` ไว้ที่ `/tmp/mnt/data/` จากการสังเกตคุณ @gartnera พยายามไม่เปลี่ยนแปลงโค้ดฉบับ ซึ่งช่วยให้ไม่ต้องแก้ไขคู่มือ

ตอนนี้พบวิธีหยุดการทำงาน watchdog เมื่อเกิดปัญหาจากซอฟต์แวร์จะช่วยให้ไม่เกิดการ Boot Loop แต่หน้าจอค้างที่โลโก้มาสด้า ทำให้นักพัฒนาใช้ Serial Console แก้ไขไฟล์ที่บกพร่องได้ ซึ่ง AIO ได้กำหนดคำสั่งหยุดทำงาน watchdog เป็นค่าเริ่มต้นเมื่อใช้ AIO ดังนั้น

### ยังมีความจำเป็นต้องใช้ `enable_input_filter` หรือไม่?
เมื่อไม่ใช้ `enable_iput_filter` ทำให้ไม่จำเป็นต้องเก็บข้อมูลใน `/tmp/mnt/data/` ดังนั้นย้าย `input_filter` ไปเก็บที่เดียวกับ `headunit` ได้ข้อดีคือ เก็บซอฟต์แวร์อยู่ในกลุ่มเดียวกันและแก้ปัญหา Factory Reset ได้

ผมจึงยกเลิกการใช้ `enable_input_filter` เพราะเป็นสาเหตุปัญหาเมื่อ Factory Reset ปัจจุบันเราทราบวิธีแก้ปัญหาเมื่อซอฟต์แวร์ไม่ทำงานได้แล้ว ด้วยการใช้ Serial console
