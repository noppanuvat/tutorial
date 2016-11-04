#Protocol Buffer
เป็นหลักการวางโครงสร้างข้อมูลในโค้ด คล้าย XML, JSON เป็นต้น โครงสร้างข้อมูลแบบ protobuf(protocol buffer) เหมาะกับข้อมูลที่อ้างถึงได้แบบเป็นลำดับ(serializing structured data) ตัวอย่างข้อมูลแบบเป็นลำดับ เช่น ข้อมูลระบุตัวบุคคลประกอบด้วยชุดข้อมูล คำนำหน้า ชื่อ สกุล ลายนิ้วมือ ภาพถ่าย เป็นต้น ชุดข้อมูลนี้อ้างอิงเป็นลำดับได้ หากไม่ใช้ protobuf ปกติจะอ้างอิงด้วย struct หรือ class เพื่อเก็บชุดข้อมูล ซึ่งมีข้อจำกัดว่าข้อมูลนั้นจะใช้ได้กับเครื่องมือพัฒนาที่เขียน แต่เมื่อใช้ protobuf จะช่วยให้รูปแบบโครงสร้างข้อมูลที่สร้างนี้ใช้ได้กับเครื่องมือพัฒนาอื่น  