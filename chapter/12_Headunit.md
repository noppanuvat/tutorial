#Headunit
หัวข้อนี้เป็นความสนใจส่วนตัว เพราะเป็นโค๊ดที่เขียนด้วย C เป็นภาษาโปรแกรมมิ่งเดียวที่พอเข้าใจบ้างเมื่อเทียบกับภาษาอื่น ก็เป็นเหตุผลเดียวจริงๆ เพราะถ้าสนใจฟังก์ชั่น Android Auto จริงๆ อาจจะใช้ tablet android เปิดใช้ android auto 2.0 หรือรออีกไม่นาน Mazda น่าจะปล่อย MZD Connect ที่รองรับ Android Auto 

โค้ดต้นฉบับเขียนโดย [@mikereidis](http://forum.xda-developers.com/member.php?u=3575078) โค้ดแรกเริ่มเก็บที่ [https://github.com/mikereidis/headunit](https://github.com/mikereidis/headunit) ต่อมาพัฒนาต่อยอด @konsulko @spadival และ @gartnera ช่วงปลายเดือนสิงหาคม 2559 Google Play Serviceได้อัพเดท ทำให้ headunit เวอร์ชั่น Mike ไม่สามารถทำงานร่วมกับ Android มีความพยายามจากนักพัฒนาหลายกลุ่มหาทางออก จนกระทั่งคุณ @borconi พบวิธีทำให้ Headunit กลับมาทำงานร่วมกับ Android ได้ปัจจุบัน(พย.2559)ยังคงทำงานได้ 
#Source code

- https://github.com/gartnera/headunit


```
├── jni
├── mazda
│   ├── m3-toolchain
│   │   ├── arm-cortexa9_neon-linux-gnueabi
│   │   ├── bin
│   │   ├── include
│   │   ├── lib
│   │   ├── libexec
│   │   └── share
│   └── mazda-connector
│       ├── android
│       ├── shared
│       └── src
└── ubuntu
```

``jni`` เก็บข้อมูลโพรโทคอลที่จำเป็นสำหรับ Android Auto 

``mazda`` เก็บโค้ดสำหรับรันบนรถ เป็นโค้ดที่เขียนเพื่อคอมไพล์กับ cmu

``ubuntu`` เก็บโค้ดสำหรับรันบนระบบปฏบัติการลินุกซ์ อันนี้เป็นข้อดีที่คุณ @spadival ได้ทำไว้ ช่วยให้การทดสอบง่ายขึ้น 

``mazda/m3-toolchain`` เป็นไดเร็คทอรี่เก็บคอมไพล์เลอร์เขียนโดย @jmgao เพิ่มใน github โดย @lmagder ช่วยให้การคอมไพล์ headunit ทำง่ายขึ้น

``mazda/mazda-connector`` เป็นไดเร็คทอรี่เก็บโปรแกรม ``input_filter`` เขียนโดย @jmgao ต่อมาคุณ @gartnera นำมาใช้กับ Headunit ทำให้ควบคุม android auto ได้จากปุ่มควบคุมบนพวงมาลัย

เริ่มที่ ``ubuntu`` ที่เคยเขียนว่ามีประโยชน์ต่อการพัฒนาเพราะไม่ต้องนำโค้ดไปทดลองบนรถจริง  สาเหตุที่ใช้ DHU(Desktop Head Unit) ไม่ได้เพราะไม่ได้ใช้โค้ดเดียวกัน การมี ``ubuntu`` จึงเป็นโค้ดที่ใช้ทดสอบ headunit  