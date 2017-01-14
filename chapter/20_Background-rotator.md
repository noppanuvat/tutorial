# เจาะโค้ด Background Rotator
บทความนี้อธิบายโค๊ด Background Rotator เริ่มจากโค้ดต้นฉบับที่มากับรถ การเปลี่ยนbackground แบบปกติ การเปลี่ยน background แบบ rotator และสุดท้าย การลองแก้โค้ด

# ไฟล์ background.png
ตำแหน่งเก็บไฟล์ใน CMU บันทึกที่ `/jci/gui/common/images/background.png` หากต้องการเปลี่ยน background เปลี่ยนไฟล์นี้ได้โดยตรง ข้อควรระวัง กำหนดขนาดไฟล์ตรงตามคุณสมบัติ ดังนั้น ไฟล์ชนิด png และความละเอียดไฟล์ 800x480pixels

# เบื้องหลัง background rotator
เปลี่ยน background ผ่าน css รู้เพราะ [[Diff ดู](https://atom.io/packages/compare-files)] วิธีแก้ css เป็นวิธีแก้ไขเว็บปกติ วิธีที่ใช้คำทำรูปภาพแนวยาว เปลี่ยน index รูปไปครั้งละ 800pixel 10ครั้ง ทำให้มีรูปยาว 8000x480px

# โค้ด background.css เลือกเฉพาะส่วนเกี่ยวข้อง
โค้ดใหม่มี 2ส่วน

```css
.CommonBgImg {
  background-image: url("../images/background.png");
  background-repeat: no-repeat;
  width: 800px;
  height: 480px;
  position: absolute;
  left: 0px;
  top: 0px;
  z-index: 0;
  background-position:0;
  animation: slide 600s steps(10,end) infinite;
}
```
และ

```css
/*Background switch animation for 10 backgrounds 8000px*/
@keyframes slide {
  to {
    background-position:-8000px;
  }
}
```

## พื้นฐาน CSS
css มีโครงสร้างหลัก 2 ส่วน `selectors` และ `declaration` ตัวอย่างรูปแบบที่พบ
```css
p {
    color: red;
    text-align: center;
}
```
มี `p` เป็น `selectors`  ภายใน '{..}' เป็น `declaration`

`selectors` ใช้เชื่อม tag HTML มี `selectors` แบบ `elements` , แบบ `id` ตัวอย่างด้านบนเป็น `element selector` ด้านล่างนี้เป็นตัวอย่าง `id selector`

```css
#para1 {
    text-align: center;
    color: red;
}
```
เมื่อต้องการค้นหา `selectors` ใช้คำสั่ง `find` หรือ `select` จากโค้ด HTML ตามด้วยชื่อ `selectors` ได้ ต้วอย่างเช่น

```html
<!DOCTYPE html>
<html>
<head>
<style>
p {
    text-align: center;
    color: red;
}
</style>
</head>
<body>

<p>Every paragraph will be affected by the style.</p>
<p id="para1">Me too!</p>
<p>And me!</p>

</body>
</html>
```

กลับไปที่โค้ด background rotator
## CSS ใน background rotator
เกี่ยวข้องคลาส `@keyframes slide`[[keyframes](https://css-tricks.com/snippets/css/keyframe-animation-syntax/)]
จากโค้ด

```css
  animation: slide 600s steps(10,end) infinite;
```
รูป 10รู

# เปลี่ยน background แบบเลื่อน
เราจะใช้ `translate3d` วิธีนี้ไม่นิยมบนเพราะใช้ CPU  ซึ่งทำให้เครื่องทำงานช้า เสนอเป็นตัวอย่างให้เห็นแนวทางการประยุกต์ css
https://www.kirupa.com/html5/animating_movement_smoothly_using_css.htm
