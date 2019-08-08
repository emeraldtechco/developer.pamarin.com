# การเข้าสู่ระบบ (Authentication)

# บทความ
- [แนวทางปฏิบัติที่ดี ในการทำ OAuth 2.0 Access Token & Refresh Token เพื่อความปลอดภัย](https://medium.com/@jittagornp/best-practice-%E0%B9%83%E0%B8%99%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%97%E0%B8%B3-oauth2-access-token-refresh-token-457ae3bee4b7)

# Endpoint
- `/oauth/authorize`
   - `response_type=code`
- `/oauth/token`
   - `grant_type=authorization_code`
   - `grant_type=refresh_token`
- `/oauth/session`

# Flow
- [Authorization Code](#authorizationcode)

# Authorization Code
![authentication.svg](./authentication.svg)

# Step
  
2.) นำ `access_token` มา build http post 
ส่งไปตรวจสอบที่ `authen.pamarin/com/oauth/session`
```
Request Method : POST
Content-Type : application/x-www-form-urlencoded
Authorization : Bearer $ACCESS_TOKEN  
```
4.1) ถ้า `access_token` valid
จะ return `user_session` กลับมาให้ในรูปแบบ json
```json
{
    "id": "df1434aa-7b81-481f-9efa-e85eb39448cd",
    "issuedAt": 1565197615855,
    "expiresAt": 1565199415855,
    "user": {
        "id": "5cff55864ca1bc12305164ba",
        "name": "นาย สมชาย ใจดี",
        "authorities": [
            "ADMIN"
        ]
    },
    "client": {
        "id": "b98e21b4-ce2a-11e7-abc4-cec278b6b50a",
        "name": "OAuth2 Test Application",
        "scopes": [
            "user:public_profile"
        ]
    }
}
```
4.2) ถ้า `access_token` invalid
จะ return error กลับไปในรูปแบบ json [คำอธิบาย error](./error/) 

```json
{
    "error": "unauthorized_client",
    "error_status": 401,
    "error_description": null,
    "error_timestamp": 1565198086100,
    "error_uri": "https://developer.pamarin.com/document/error/",
    "error_code": "eb7852128224cb93",
    "error_fields": [],
    "state": null
}
```
