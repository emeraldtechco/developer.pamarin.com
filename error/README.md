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
  "error_timestamp": 1560939554466,
  "error_uri": "https://developer.pamarin.com/error/",
  "error_code": "31db055ecac97230",
  "error_fields": [],
  "state": null
}
```
- `error` (String) คือ ประเภท error ของระบบ
- `error_status` (String) คือ http status error code 
- `error_description` (String) คือ message ที่อธิบายว่า error นี้เกิดจากอะไร  
- `error_timestamp` (Number) คือ เวลา timestamp ที่เกิด error
- `error_uri` (String) คือ url เอกสารสำหรับอธิบาย error 
- `error_code` (String) คือ รหัส (`trace_id`) ไว้ใช้สำหรับ track ต้นตอของ error ที่เกิดขึ้น 
- `error_fields` (Array) ใช้สำหรับกรณี validate input form แล้วเกิด error ขึ้นที่ field ใด field หนึ่ง
- `state` (String) คือ validation state ของ client เพื่อใช้ป้องกัน csrf ซึ่ง server จะส่งกลับไปด้วยกรณีเกิด error (ถ้า client ส่ง parameter นี้มา)

# รายะเอียด (Details)

### error

> ประเภท error ของระบบ

- type : String
- nullable : Not Null

เช่น 
- `unauthorized` คือ ยังไม่ได้ทำการ sign-in เข้าสู่ระบบ  
- `unauthorized_client` คือ ยังไม่ได้ทำการ sign-in เข้าสู่ระบบ   
- `access_denied` คือ ไม่มีสิทธิ์เข้าถึง
- `user_locked` คือ บัญชีผู้ใช้ถูกล็อค (Lock)
- `invalid_request` คือ คำร้องขอไม่ถูกต้อง  
- `invalid_csrf_token` คือ คำร้องขอไม่ถูกต้อง ตาม flow ที่กำหนด  
- `invalid_same_origin` คือ คำร้องขอไม่ถูกต้อง (url ที่เรียกใช้ ไม่ได้อยู่ในรายการที่อนุญาต) 
- `invalid_signature` คือ ข้อมูลที่ส่งมา ไม่ตรงกับ signature (ลายเซ็นอิเล็กทรอนิกส์) ที่ส่งมาด้วย 
- `invalid_username_password` คือ บัญชีผู้ใช้ หรือรหัสผ่าน ไม่ถูกต้อง
- `invalid_scope` คือ scope ไม่ถูกต้อง  
- `invalid_recovery_code` คือ รหัสกู้คืน หรือ recovery code ไม่ถูกต้อง 
- `invalid_client` คือ แอพพลิเคนชั่น (Application) หรือ Client ที่ใช้เชื่อมต่อ ไม่ถูกต้องหรือได้รับอนุญาต  
- `unsupported_response_type` คือ ไม่รองรับ response type  
- `not_found` คือ ไม่พบข้อมูลที่ร้องขอ 
- `rate_limit` คือ ใช้งานเกิน limit ที่กำหนดไว้ 
- `server_error` คือ ระบบประมวลผลผิดพลาด  
