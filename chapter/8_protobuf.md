#Protocol Buffer version 3

``protoc`` โปรแกรมแปลงไฟล์นามสกุล .proto เป็นโค๊ดภาษาคอมพิวเตอร์เช่น c++ java php javascript ประโยชน์เพื่อให้มีโครงสร้างไฟล์เป็นตัวกลางมีรูปแบบแน่นอน เพื่อในอนาคตมีการเปิดตัวแปรหรือเปลี่ยนแปลงโครงสร้างทำได้ด้วยการแปลงผ่าน protoc แล้ว compile ใหม่ช่วยแก้ไขโค้ดน้อยลง

protobuf(Protocol Buffer) ทำหน้าที่จัดระเบียบการเขียนโปรแกรมให้มีรูปแบบชัดเจนขึ้น และได้เพิ่มคุณสมบัติการทำงานร่วมกับภาษาคอมพิวเตอร์ได้หลากหลาย การใช้ protobuf ช่วงแรกอาจจะใช้การแปลงโค๊ดเดิมให้เข้ารูปแบบ protobuf ประโยชน์ที่ได้รับคือทำความเข้าใจโค๊ดได้ง่ายขึ้น จากการอ่าน .proto ทำให้เห็นโครงสร้างตัวแปรที่สำคัญ

protobuf เป็นเทคนิคหนึ่งของการเขียนโปรแกรม จะมีหรือไม่มีก็ได้ สัมผัสที่ผมได้อ่านโคีดที่มีการใช้ protobuf จะทำให้เห็นถึงทักษะของโปรแกรมเมอร์ที่คำนึงถึงการต่อยอด

เป็นหลักการวางโครงสร้างข้อมูลในโค้ด คล้าย XML, JSON เป็นต้น โครงสร้างข้อมูลแบบ protobuf(protocol buffer) เหมาะกับข้อมูลที่อ้างถึงได้แบบเป็นลำดับ(serializing structured data) ตัวอย่างข้อมูลแบบเป็นลำดับ เช่น ข้อมูลระบุตัวบุคคลประกอบด้วยชุดข้อมูล คำนำหน้า ชื่อ สกุล ลายนิ้วมือ ภาพถ่าย เป็นต้น ชุดข้อมูลนี้อ้างอิงเป็นลำดับได้ หากไม่ใช้ protobuf ปกติจะอ้างอิงด้วย struct หรือ class เพื่อเก็บชุดข้อมูล ซึ่งมีข้อจำกัดว่าข้อมูลนั้นจะใช้ได้กับเครื่องมือพัฒนาที่เขียน แต่เมื่อใช้ protobuf จะช่วยให้รูปแบบโครงสร้างข้อมูลที่สร้างนี้ใช้ได้กับเครื่องมือพัฒนาอื่น

ข้อมูลสื่อสารผ่านเครือข่ายมีรูปแบบ binary เป็นลำดับข้อมูลเช่นการสื่อสารผ่านโพรโทคอลต่าง ข้อมูลที่สื่อสารนั้นมีรูปแบบการส่งคงที่ มีลำดับชุดข้อมูล และความยาว ซึ่งโพรโทคอลส่วนใหญ่กำหนดขนาด field คงที่ โค้ดดั้งเดิมใช้การกำหนดขนาดคงที่อยู่ในโค้ด ซึ่งสั้นและรวดเร็ว แต่ปัญหาจะเกิดขึ้นเมื่อต้องการปรับปรุง field เช่นเปลี่ยนขนาด field หรือเพิ่มจำนวน field จึงเป็นที่มาของการใช้ protocol buffer ช่วยแก้ปัญหาเพียงแก้ไข \*.proto แล้ว คอมไพล์ใหม่ จะใช้ field ใหม่ได้โดยไม่ต้องแก้ไขโค้ดหลัก ซึ่งการแก้ \*.proto  จะส่งผลกับทุกโค้ดที่เรียกไฟล์นี้

#Applied
##ติดตั้งโปรแกรม

- ติดตั้ง protobuf https://github.com/google/protobuf

##ตัวอย่าง C++
https://developers.google.com/protocol-buffers/docs/cpptutorial


##การทำงาน
กำหนด Protocol Format ตั้งชื่อไฟล์ sample.proto มีรูปแบบตาม protobuf กำหนด ตัวอย่างเช่น

* hu.proto

```protobuf
syntax = "proto3";

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
}
```

### คำสั่งคอมไพล์ไฟล์ proto

```bash
protoc --cpp_out=generated hu.proto
```
### ไฟล์ hu.bp.c และ hu.bp.c
เมื่อคอมไพล์ผ่าน protoc ทำให้ใช้ฟังก์ชั่นติดต่อโครงสร้างข้อมูลที่ Google ได้เตรียมไว้ เช่น ตั้งชื่อตัวแปร `name` มีคำสั่ง set_name() , get_name() เพื่อเขียนและอ่าน เมื่ออ่านโค้ดที่เขียนด้วย protobuf จะอ่านไฟล์ proto เห็นรายการตัวแปรจะทราบว่าจะเข้าถึงตัวแปรนั้นได้อย่างไร

### ลองเล่น
น่าจะดีถ้ามีตัวอย่างง่ายๆ ถ้าเริ่มได้น่าจะช่วยเข้าใจขึ้น(พูดกับตัวเอง) ผมสังเกตโปรแกรมเมอร์ท่านหนึ่งบอกว่าไม่เข้าใจ protobuf ยังชอบเขียนโปรแกรมอ่านทีละไบต์ ต่อมาไม่นานเขาสนใจเขียน protobuf ได้ในเวลาไม่กี่วัน ทำผมแปลกใจเพราะตนเองเห็นข้อดีของ protobuf และพยายามศึกษา แต่ไม่ได้เริ่มโค้ดเสียที อาจเป็นสาเหตุทำให้ผมไม่เข้าใจ หัวข้อนี้จึงเลือกวิธี่โค้ด

* exam.proto
```protobuf

```

# Learning
## proto file syntax
มีส่วน preamble และ message
- preamble ใช้กำหนดข้อมูลเกี่ยวข้องกับโปรแกรม protocol buffer เช่น เลขเวอร์ชั่น
- message ใช้กำหนดข้อมูลที่จะนำไปใช้ เช่น ชื่อตัวแปร ค่าของตัวแปร และ ลำดับการอ้างถึง

ตัวอย่าง

```protobuf
syntax = "proto3";

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
}
```

### Preamble
จากตัวอย่าง syntax อยู่ในส่วน preamble ใช้กำหนดเลขเวอร์ชั่นที่รัน protocol buffer ปัจจุบันเวอร์ชั่น 3 (proto3) ถ้าไม่กำหนดค่าพื้นฐานกำหนดเป็น เวอร์ชี่น2 (proto2) ควรกำหนดให้อยู่บันทัดที่ 1 เสมอ

### Message
จากตัวอย่างส่วน Meessage กำหนด มีรูปแบบ กำหนดกลุ่มตัวแปรด้วย message แล้วตามด้วยชื่อกลุ่มตัวแปร ลักษณะตัวแปรมีโครงแบบคำสั่ง ``struct`` ใน C

```protobuf
message Foo {
 string bar1 = 1;
 int32  bar2 = 2;
}
```

จากตัวอย่างด้านบน กำหนดชื่อข้อมูล `Foo` มีฟิลด์ 2 รายการ เป็นชนิด `string` และ ชนิด `int32` แต่ละฟิลด์จะต้องมีลำดับเพื่อใช้อ้างถึง ปกติกำหนดเรียกลำดับเริ่มต้น 1 ในตัวอย่างกำหนดเริ่มต้น `1` และตามด้วย `2` ทำความเข้าใจส่วนนี้ด้วยการคิดถึง protocol frame format เช่น ipv6 frame มีรูปแบบในรูป ![https://notes.shichao.io/tcpv1/ch5/](https://notes.shichao.io/tcpv1/figure_5-2_600.png)
มีลำดับ

- Version
- DSField
- ECN
- Flow Label
- Payload Length
- Next Header
- Hop Limit
- Source IP Address
- Destination IP Address
- Payload

ตัวอย่าง message

```protobuf
message IPv6 {
 int32 Version = 1;
 int32 DSField = 2;
 int32 ECN = 3;
 int32 FlowLabel = 4;
 int32 PayloadLength = 5;
 int32 NextHeader = 6;
 int32 SrcIP = 7;
 int32 DestIP = 8;
 bytes Payload = 9;
}
```
ตัวอย่างชนิดข้อมูลที่เป็นไปได้ https://developers.google.com/protocol-buffers/docs/proto3#scalar

## output file usage
หลังจากคอมไพล์ไฟล์ proto จะทำให้ได้โค้ดตรงตามภาษาคอมพิวเตอร์ที่ได้กำหนดไว้ขั้นตอนคอมไพล์เช่น คอมไพล์เป็น c++ ใช้คำสั่ง
```
protoc --cpp_out=generated hu.proto
```
ไฟล์บันทึกในไดเร็คทอรี่ generated มีรูปแบบไฟล์เป็นไฟล์ c++ ทำได้ไฟล์
- hu.pb.cc
- hu.pb.h

เรียกไฟล์ในโค้ดภาษา c++ ด้วยคำสั่ง

```c++
#include "generated/hu.pb.h"
```


#แปล
แนะนำการใช้ C++ ร่วม protobuf อธิบายด้วยโค้ดตัวอย่าง เมื่อจบบทเรียนนี้ท่านจะเข้าใจ

* การเขียนไฟล์ .proto
* การใช้ protocol buffer
* การใช้ API เขียน/อ่าน ผ่าน C++

## ไฟล์ตัวอย่าง
ดาวน์โหลดที่ https://github.com/google/protobuf/tree/master/examples ไฟล์ใช้ในตัวอย่างนี้

* addressbook.proto
* add_person.cc
* list_people.cc

## คอมไพล์
```
protoc --cpp_out=. addressbook.proto
g++ `pkg-config --libs protobuf` add_person.cc addressbook.bp.cc
```
