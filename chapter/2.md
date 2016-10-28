# Application Lifecyles
แปลจาก [https://github.com/flyandi/mazda-custom-application-sdk/wiki/Get-Started](https://github.com/flyandi/mazda-custom-application-sdk/wiki/Get-Started)

Lifecycles ที่สำคัญมี 3 เหตุการณ์ creates,focused และ lost

# Lifecycles
ใช้กำหนดเหตุการณ์ (events) เมื่อมีการเปลี่ยนสถานะ เข้าใจว่าเป็นเสมือนการเขียนโปรแกรมที่ทำงานผ่านเหตุการณ์ (event driven) เมื่อผู้เขียนเข้าใจการทำงาน nodejs, jquery จะกลับมาเขียนเนื้อหาส่วนนี้อีกครั้ง

## created
```created``` เริ่มทำงานเมื่อถูกเรียกครั้งแรก 

* ตัวอย่างโค้ด

```javascript
  created: function() {
    this.label = $("<label/>").html("Hello World").appendTo(this.canvas);
  },
```  
## focused
โค้ดใน `focused` เริ่มทำงานเมื่อเกิดเหตุการณ์ถูกสั่งเช่น กดเลือกจากเมนู หรือมีการกดปุ่ม

* ตัวอย่างโค้ด 

```javascript
  focused: function() {
    this.label.html("Got the focus");
  }
```

## lost
โค้ดใน `lost` เริ่มทำงานเมื่อ `focused` ทำงานจบ

* ตัวอย่างโค้ด

```javascript
  lost: function() {
    this.label.html("");
  }
```

# โปรแกรม Hello, world

สำหรับ windows ใช้วิธี copy ตามปกติได้ (บางทีผมก็คิด ว่าจะเขียนอธิบายเรื่องง่ายๆ นี้ดีไหม บางทีไม่เขียนอาจจะช่วยเข้าใจง่าย) ขั้นตอนนี้เป็นการฝึกทักษะ อาจจะต้องเรียนรู้ด้วยการลงมือทำและทำความเข้าใจ

1. สร้างไฟล์ที่จำเป็นทั้งหมด 3ไฟล์ `app.js`, `app.css` และ `app.png` ใช้คำสั่งสร้างไฟล์ด้านล่างนี้

```bash
npm install -g casdk
casdk create myapp
```
หรือใช้วิธี copy ไฟล์ตัวอย่างใน `apps` แล้วแก้ไขก็ได้ สมมติว่าเลือก copy ไฟล์ ไปที่ไดเร็คทอรี่ `apps` แล้วพิมพ์คำสั่งนี้

```bash
cp -r app.helloworld app.helloworld2
```
.
2. ลองแก้โค้ดไฟล์ `app.js` แล้วกลับไปดู simulators

