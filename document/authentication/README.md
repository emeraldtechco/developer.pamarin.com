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
- [Authorization Code](#authorization-code)

# Authorization Code
![authentication.svg](./authentication.svg)

# Step

### Step 1) 
User พยายามเข้าใช้งาน `www.pamarin.com` (มองเป็น Client / Resource Server) ผ่านทาง browser      
โดย browser จะแนบ http cookie : `access_token` / `refresh_token` ไปพร้อมกับ request  
  
### Step 2) 
Client / Resource Server จะนำ `access_token` มา build http post   
ส่งไปตรวจสอบที่ `authen.pamarin/com/oauth/session`  
Http headers  
```
Request Method : POST
Content-Type : application/x-www-form-urlencoded
Authorization : Bearer $ACCESS_TOKEN  
```
### Step 3)
Authoriation Server จะทำการ verify `access_token` และ `user_session` 
ที่เก็บไว้ใน database (redis) ว่ายัง valid อยู่หรือไม่   

### Step 4.1) 
ถ้า `access_token` และ `user_session` บน Authorization Server ยัง valid อยู่    
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

### Step 4.1.1) 
Client / Resource Server จะเช็คสิทธิ์ (authorities) ตามข้อมูล 
`user_session` ที่ระบบ authen (Authorization Server) ส่งมาให้  

### Step 4.2) 
ถ้า `access_token` หรือ `user_session` invalid (ไม่ valid)   
Authorization Server จะ return error กลับไปในรูปแบบ json ([คำอธิบาย error](./../error/)) 

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
### Step 5)  
นำ `refresh_token` มา build http post (ต่อจาก 4.2)  
เพื่อขอ `access_token` ใหม่  
ส่งไปที่ `authen.pamarin.com/oauth/token` (grant_type=refresh_token)  
Http headers  
```
Request Method : POST
Content-Type : application/x-www-form-urlencoded  
```
Http Body
```
client_id : $CLIENT_ID  
client_secret : $CLIENT_SECRET  
grant_type : refresh_token  
redirect_uri : ""
refresh_token : $REFRESH_TOKEN  
```
