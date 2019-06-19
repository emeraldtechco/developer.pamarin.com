# ข้อผิดพลาด (Error) 

> การจัดการข้อผิดพลาดต่าง ๆ ในระบบ

# ตัวอย่าง
> [https://api.pamarin.com/v1/errors](https://api.pamarin.com/v1/errors)

# Format 
ทุก ๆ Service ของ Pamarin จะแสดง `error` โดยมีรูปแบบดังต่อไปนี้

```json
{
  "error": "unauthorized",
  "error_status": 401,
  "error_description": "Require access token in http header \"Authorization\" : \"bearer $TOKEN\"",
  "error_timestamp": 1560937742447,
  "error_uri": null,
  "error_code": "fba3e30dc69e249c",
  "error_fields": [],
  "state": null
}
```
- `error` คือ ประเภท error ของระบบ
- `error_status` คือ http status error code 
- `error_description` คือ message ที่อธิบายว่า error นี้เกิดจากอะไร  
- `error_timestamp` คือ เวลา timestamp ที่เกิด error
- `error_uri` คือ uri เอกสารสำหรับอธิบาย error 
- `error_code` คือ รหัสไว้ใช้สำหรับ track ต้นตอของ error ที่เกิดขึ้น 
- `error_fields` ใช้สำหรับกรณี validate input form แล้วเกิด error ขึ้นที่ field ใด field หนึ่ง
- `state` คือ validation state ของ client เพื่อใช้ป้องกัน csrf ซึ่ง server จะส่งกลับไปด้วยกรณี error