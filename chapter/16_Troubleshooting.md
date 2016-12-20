# กรณีตัวอย่าง
เกิดหน้าจอค้างหลังเปลี่ยน background ใช้ Serial Console เชื่อม CMU ตรวจพบการใช้เนื้อที่ /root พบเนื้อที่เต็ม ได้ลบข้อมูลบางส่วนจนเหลือพื้นที่เพียงพอต่อการกู้ไฟล์ sm.conf และ background.png จนทำให้หน้าจอบูทได้ ต่อมาใช้ AIO(ไม่ทราบเวอร์ชั่น) สร้างไฟล์ uninstallทุกปลั๊กอิน หลังรีบูทเกิด reboot loop ได้แก้ไขให้หยุด reboot loop ได้สำเร็จ แต่หน้าจอยังค้าง

# ชี้แจง
ถ้าเป็นปัญหาที่ค้นคำตอบได้จากอินเทอร์เน็ตผมจะสรุปเป็นลำดับขั้นและอธิบายว่าแก้ไขได้ แต่ถ้าเป็นปัญหาที่ไม่เคยพบหรือค้นไม่พบ(ไม่มีคนแชร์)ทำให้แก้ยากเช่นปัญหานี้ จะช่วยเหลือเท่าที่ทำได้ คำสั่งด้านล่างนี้ไม่ใช่คำสั่งแก้ปัญหาแบบที่เดียวจบ แต่เป็นการทำความเข้าใจปัญหาแล้วแก้ไขทีละจุดโดยใช้ความรู้พื้นฐานคำสั่งลินุกซ์ อ้างอิงจากข้อมูลที่ให้มาและความรู้ที่ผมมี

# แนวทาง
ใช้วิธีตามแก้ไขทีละไฟล์ ไม่แนะนำให้ copy /jci ทับไฟล์เก่า เพราะทำให้ไฟล์ในระบบถูกแก้ไขเวลา(timestamp) นอกจากแก้ปัญหาไม่ได้แล้วยังทำให้ปัญหายากขึ้น

1. ค้นหาไฟล์ที่ถูกแก้ไขล่าสุด
2. กู้ไฟล์ที่ถูกแก้ไขล่าสุด ยกเว้น sm.conf เพราะถูกแก้ไขเพื่อป้องกัน reboot loop
3. หลังจากกู้ไฟล์ ทดลอง reboot ถ้ายังไม่ได้ กลับไปทำข้อ1 แก้ไฟล์ใหม่ จนกว่าจะเข้าหน้าหลักได้

คำสั่งค้นไฟล์ที่ถูกแก้ไขล่าสุด ใช้[ค้นหาไฟล์เรียงตามวัน](### ค้นหาไฟล์เรียงตามวัน) เมื่อได้รายการไฟล์มาแล้วให้กู้คืนทีละไฟล์ จนกว่าจะบูทได้

## เตรียมพร้อม
- ไฟล์ backup jci เฟิร์มแวร์ 56.00.240B หรือขอจากสมาชิกท่านอื่น
- กู้ไฟล์ `sm.conf` ([กู้ไฟล์ sm.conf](#กู้ไฟล์ sm.conf))

# รวมคำสั่ง command line

### ค้นหาไฟล์นามสกุล 'org'
AIO สร้างไฟล์สำรองมีนามสกุล org เมื่อแก้ไขไฟล์ เช่น ไฟล์ sm.conf มีไฟล์สำรองชื่อ sm.conf.org ใช้วิธีนี้ค้นหาและเลือกกู้ไฟล์ได้เร็ว
```bash
find / -type f -name '*.org' 2>/dev/null
```
ถ้าไฟล์มีมาก ใช้คำสั่ง `| head -n 10` เพื่อให้แสดงผล 10บรรทัด
```bash
find / -type f -name '*.org' 2>/dev/null|head -n 10
```
### ค้นหาไฟล์เรียงตามวัน
จำวันที่แก้ไขไฟล์ได้แต่จำชื่อไฟล์ไม่ได้ ใช้คำสั่งด้านล่างเพื่อช่วยย่นเวลาค้นไฟล์ที่เคยแก้ไข
เช่น วันที่ 1ธค2559 เวลา 10:00น. แปลงเป็น YYYYMMDDhhmm => 201612011000

```bash
touch -t 201612011000 dummy.txt
find /jci -newer dummy.txt -type f -exec /bin/ls -l {} \; 2>/dev/null
```

### เปรียบเทียบไฟล์
เปรียบเทียบไฟล์ sm.conf กับ sm.conf.org ถ้าเหมือนกันจะไม่มีข้อความ เครื่องหมาย "-", "+" หมายถึงข้อความที่ต่างกัน "-" หมายถึงมีใน sm.conf.org แต่ไม่มีใน sm.conf

```bash
diff -Naur sm.conf sm.conf.org
```

ตัวอย่าง
```bash
/jci/sm # diff sm.conf sm.conf.org
--- sm.conf
+++ sm.conf.org
@@ -21,7 +21,6 @@

     <!--  Service description format: name path [args] [priority] [depends] [retry] -->
     <services type="process" reset_board="no" retry_count="0" sched_policy="SCHED_OTHER" sched_priority="0" autorun="yes" affinity_mask="0x01" ping_timeout="8000" stop_timeout="3000" save_at_shutdown="no">
-        <service type="process" name="input_filter" path="/tmp/mnt/data_persist/dev/mazda-connector/input_filter" autorun="yes" reset_board="no" retry_count="0" affinity_mask="0x02"/>

         <!-- ======================= PID waiting Services =============================================== -->

@@ -95,7 +94,6 @@
         <service type="jci_service" name="devices" path="/jci/devices/svc-com-jci-cpp-devices.so" autorun="yes" retry_count="0" args="" reset_board="yes" affinity_mask="0x02">
             <dependency type="service" value="stage_1"/>
             <dependency type="service" value="settings"/>
-            <dependency type="service" value="input_filter"/>
         </service>

         <service type="jci_service" name="jciNativegui" path="/jci/nativegui/svcjcinativegui.so" autorun="yes" retry_count="6" args="" reset_board="no" affinity_mask="0x02">
```

### กู้ไฟล์
กู้ไฟล์คือเอาไฟล์เก่าไปทับไฟล์ใหม่ ไฟล์เก่าได้จากไฟล์ที่เคย backup ไว้ ตัวอย่าง กู้ไฟล์ `opera.ini.org` เป็นไฟล์ `opera.ini`
```bash
cp -a /jci/opera/opera_home/opera.ini.org /jci/opera/opera_home/opera.ini
```

### เซต owner
เฟิร์มแวร์ 56.00.100/230/240 มี owner "1000" อยู่ในกลุ่ม
