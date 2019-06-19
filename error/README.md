# ข้อผิดพลาด (Error) 

> การจัดการข้อผิดพลาดต่าง ๆ ในระบบ

# ตัวอย่าง
> [https://api.pamarin.com/v1/errors](https://api.pamarin.com/v1/errors)

# Format 
ทุก ๆ Service ของ Pamarin จะแสดง `error` โดยมีรูปแบบดังต่อไปนี้

```json
{
  "error": "unauthorized",
  "error_type": "AuthenticationException",
  "error_timestamp": 1560932838854,
  "error_status": 401,
  "error_description": "Require access token in http header \"Authorization\" : \"bearer $TOKEN\"",
  "error_uri": null,
  "state": null,
  "error_code": null,
  "error_fields": []
}
```
