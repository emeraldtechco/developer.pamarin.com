# ข้อผิดพลาด (Error) 

> การจัดการข้อผิดพลาดต่าง ๆ ในระบบ

# ตัวอย่าง
> [https://api.pamarin.com/v1/errors](https://api.pamarin.com/v1/errors)

# Format 
ทุก ๆ Service จะแสดง `error` โดยมีรูปแบบดังต่อไปนี้

```json
{
  "error": "unauthorized",
  "error_status": 401,
  "error_description": "Require access token in http header \"Authorization\" : \"bearer $TOKEN\"",
  "error_timestamp": 1560939554466,
  "error_uri": "https://developer.pamarin.com/document/error/",
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
- `error_code` (String) คือ รหัส (`trace_id`) สำหรับผู้ดูและระบบ (Admin) หรือผู้พัฒนา ไว้ค้นหาตำแหน่งของ error ที่เกิดขึ้นในระบบ 
- `error_fields` (Array of Object) ใช้สำหรับกรณี validate input form แล้วเกิด error ขึ้นที่ field ใด field หนึ่ง
- `state` (String) คือ validation state ของ client เพื่อใช้ป้องกัน csrf ซึ่ง server จะส่งกลับไปด้วยกรณีเกิด error (ถ้า client ส่ง parameter นี้มา)

# รายะเอียด (Details)

# error

> ประเภท error ของระบบ

- type : `String`
- nullable : Not Allow 

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
- `invalid_client` คือ แอพพลิเคนชั่น (Application) หรือ Client ที่ใช้เชื่อมต่อ ไม่ถูกต้องหรือได้รับอนุญาต เช่น พารามิเตอร์ `client_id` ที่ส่งมาผิด เป็นต้น 
- `unsupported_response_type` คือ ไม่รองรับ response type  
- `not_found` คือ ไม่พบข้อมูลที่ร้องขอ 
- `rate_limit` คือ ใช้งานเกิน limit ที่กำหนดไว้ 
- `server_error` คือ ระบบประมวลผลผิดพลาด  

# error_status

> http status error code 

- type : `Number` 
- nullable : Not Allow   

เช่น 
- `400` คือ Bad Request คำร้องขอไม่ถูกต้อง ไม่เป็นไปตามข้อกำหนด 
- `401` คือ Unauthorized ไม่ได้ login หรือ ระบุตัวตนเข้าสู่ระบบ 
- `403` คือ Forbidden หรือ Access Denied ไม่มีสิทธิ์เข้าถึง 
- `404` คือ Not Found ไม่พบข้อมูล 
- `405` คือ Method Not Allowed ไม่รองรับ http method ที่ส่งมา
- `406` คือ Not Acceptable ไม่ยอมรับ http request นี้ 
- `415` คือ Unsupported Media Type ไม่รองรับ content-type ตามที่ส่งมา 
- `429` คือ Too Many Requests จำนวนคำขอมากเกินไป 
- `500` คือ Internal Server Error ระบบประมวลผลผิดพลาด   
- `503` คือ Service Unavailable ระบบปลายทางล่ม หรือปิดปรับปรุง 

Reference : [https://developer.mozilla.org/th/docs/Web/HTTP/Status](https://developer.mozilla.org/th/docs/Web/HTTP/Status)

# error_description 

> message ที่อธิบายว่า error นี้เกิดจากอะไร

- type : `String`
- nullable : Allow

เช่น

- Please login 
- Require access token in http header \"Authorization\" : \"bearer $TOKEN\" 
- Access denied  
- Not found 
- Path not found 
- Invalid Username or Password 
- User Locked 

# error_timestamp  

> เวลา timestamp ที่เกิด error  

- type : `Number`
- nullable : Not Allow

# error_uri  

> url เอกสารสำหรับอธิบาย error  

- type : `String`
- nullable : Allow  

เช่น  

- [https://developer.pamarin.com/document/error/](https://developer.pamarin.com/document/error/) 

# error_code 

> รหัส (`trace_id`) สำหรับผู้ดูและระบบ (Admin) หรือผู้พัฒนา ไว้ค้นหาตำแหน่งของ error ที่เกิดขึ้นในระบบ

- type : `String`
- nullable : Allow   

เช่น 
- a01120592219db7e  
- 31db055ecac97230 
- 2d18884122fa6d0b 

# error_fields 

> ใช้สำหรับกรณี validate input form แล้วเกิด error ขึ้นที่ field ใด field หนึ่ง  

- type : `Array of Object`
- nullable : Not Allow (default empty array)  

ตัวอย่าง

```json
{
  "error": "invalid_request",
  "error_code": "4afed893447f585c",
  "error_description": "Validate fail",
  "error_status": 400,
  "error_timestamp": 1560957259332,
  "error_uri": "https://developer.pamarin.com/document/error/",
  "error_fields": [
    {
      "name": "email", 
      "code": "AvailableEmail", 
      "description": "not available"
    }
  ],  
  "state": "8wC04d/1cW6bE5M="
}
```
- `error_fields[].name` (String) คือ ชื่อ field ที่เกิด error  
- `error_fields[].code` (String) คือ ประเภท error ของ field นั้น ๆ เช่น 
  - `not_null` (จะต้องไม่เป็น null) 
  - `not_blank` จะต้องไม่เป็น empty string
  - `length` จะต้องมีความยาวตามที่กำหนด 
- `error_fields[].description` (String) คือ คำอธิบาย error field นั้น ๆ
