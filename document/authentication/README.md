# การเข้าสู่ระบบ (Authentication)

[www.pamarin.com](https://www.pamarin.com) ใช้วิธีการ Authen ด้วย OAuth 2.0 ซึ่งเป็น Standard Authorization Framework ตามข้อกำหนด RFC6749 ที่ทาง IETF (Internet Engineering Task Force) สร้างขึ้นในปี 2012 มีระบบดัง ๆ อย่าง Google, Facebook, Twitter, Youtube etc. ใช้ ทำให้ระบบต่าง ๆ สามารถเชื่อมต่อเข้ามายัง Pamarin เพื่อขอใช้งาน Resources หรือ APIs ต่าง ๆ ที่ทาง Pamarin ได้เตรียมไว้ให้ได้ โดยการปฏิบัติตามข้อกำหนดต่าง ๆ ตามเอกสารนี้  

*** หมายเหตุ : ทาง Pamarin ได้มีการปรับแต่ง OAuth เพิ่มเติมเพื่อให้รองรับและเหมาะสมกับธุรกรรมต่าง ๆ ของ Pamarin มากขึ้น 

# Version

> BETA 

# อ้างอิง 
- [RFC 6749 - The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749)
- [https://oauth.net/2/](https://oauth.net/2/)  
- [https://www.oauth.com/](https://www.oauth.com/)  

# บทความ
- [แนวทางปฏิบัติที่ดี ในการทำ OAuth 2.0 Access Token & Refresh Token เพื่อความปลอดภัย](https://medium.com/@jittagornp/best-practice-%E0%B9%83%E0%B8%99%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%97%E0%B8%B3-oauth2-access-token-refresh-token-457ae3bee4b7)
- [[บันทึกส่วนตัว] : การออกแบบ ระบบ authentication ของ micro service](https://medium.com/@jittagornp/%E0%B8%9A%E0%B8%B1%E0%B8%99%E0%B8%97%E0%B8%B6%E0%B8%81%E0%B8%AA%E0%B9%88%E0%B8%A7%E0%B8%99%E0%B8%95%E0%B8%B1%E0%B8%A7-%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%AD%E0%B8%AD%E0%B8%81%E0%B9%81%E0%B8%9A%E0%B8%9A-%E0%B8%A3%E0%B8%B0%E0%B8%9A%E0%B8%9A-authentication-%E0%B8%82%E0%B8%AD%E0%B8%87-micro-service-c08bfea025ef)

# Endpoint

> https://authen.pamarin.com

- `/oauth/authorize`
   - `response_type=code` - สำหรับ Grant Authorize และขอ Authorization Code 
- `/oauth/token`
   - `grant_type=authorization_code` - สำหรับขอ Access Token จาก Authorization Code  
   - `grant_type=refresh_token` - สำหรับขอ Access Token จาก Refresh Token  
- `/oauth/session` - สำหรับ Validate Access Token และ Get ข้อมูล User Session 
- `/oauth/signout` - สำหรับ Signout หรือ Logout ออกจากระบบ  

# Flow
- [Authorization Code](#authorization-code)

# Authorization Code
![authentication-flow.svg](./authentication-flow.svg)

# Step

- ### Step 1) - User / Browser     
พยายามเข้าใช้งาน หรือ ร้องขอ Resource จาก `www.pamarin.com` (Client / Resource Server) ผ่านทาง browser      
โดย browser จะแนบ http cookie : `access_token` / `refresh_token` ไปพร้อมกับ request  
  
- ### Step 2) - Client / Resource Server  
จะนำ `access_token` มา build http post   
ส่งไปตรวจสอบที่ Authorization Server `/oauth/session`  
  
Http headers  
```
Request Method : POST
Content-Type : application/x-www-form-urlencoded
Authorization : Bearer $ACCESS_TOKEN  
```
*** หมายเหตุ : ถ้าไม่มี `access_token` ส่งมาจาก browser จะกระโดดไปทำข้อ 5 เลย 

- ### Step 3) - Authorization Server : `/oauth/session`  
จะทำการ verify `access_token` และ `user_session` 
ที่เก็บไว้ใน data store (redis) ว่ายัง valid อยู่หรือไม่   

- ### Step 4.1) - Authorization Server
ถ้า `access_token` และ `user_session` ยัง valid อยู่    
จะ return `session_token` เป็น jwt กลับมาให้ในรูปแบบ json
  
Response Body    
```json
{
    "session_token" : "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJzZXNzaW9uLnVzZXIuaWQiO.."     
} 
```

- ### Step 4.1.1) - Client / Resource Server
ทำการ verify `session_token` ด้วย public key จากนั้นเช็คสิทธิ์ (authorities) ตามข้อมูล 
`session_token` ที่ระบบ authen (Authorization Server) ส่งมาให้  

- ### Step 4.1.1.1) - Client / Resource Server
ถ้าไม่มีสิทธิ์เข้าถึง จะ return error (`access denied`) กลับไปหา user   

- ### Step 4.1.1.2) - Client / Resource Server 
ถ้ามีสิทธิ์เข้าถึง จะ return resource กลับไปหา user ตามคำร้องที่ browser ส่งมา 

- ### Step 4.2) - Authorization Server  
ถ้า `access_token` หรือ `user_session` invalid (ไม่ valid)   
จะ return error กลับไปในรูปแบบ json ([คำอธิบาย error](./../error/)) 
  
Response Body  
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
- ### Step 5)  
นำ `refresh_token` มา build http post (ต่อจาก 4.2)  
เพื่อขอ `access_token` ใหม่  
ส่งไปที่ Authorization Server  `/oauth/token` (grant_type=refresh_token)  
  
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
*** หมายเหตุ : ถ้าไม่มี `refresh_token` ส่งมาจาก browser จะกระโดดไปทำข้อ 8 เลย 

- ### Step 6) - Authorization Server : `/oauth/token`   
ทำการ verify request โดยตรวจสอบ `refresh_token` และ `user_session` ว่ายังคง valid อยู่หรือไม่  
พร้อมทั้งตรวจสอบ `client_id` กับ `client_secret` ว่าถูกต้อง มีสิทธิ์ขอ `access_token` ใหม่หรือไม่ 

- ### Step 7.1) - Authorization Server
ถ้า `refresh_token` และ `user_session` valid
จะ return `access_token`, `refresh_token` ใหม่ + ข้อมูล `session_token` เป็น jwt กลับไปในรูปแบบ json
  
Response Body
```json
{
    "access_token" : "MjMwZDk5NTAtYzZkZC00Mjg2LTg2ODUtY2EzNGJlN...",
    "token_type" : "bearer",
    "expries_in" : 1800,
    "refresh_token" : "MjMwZDk5NTAtYzZkZC00Mjg2LTg2ODUtY2EzNGJlNW...",
    "session_token" : "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJzZXNzaW9uLnVzZXIuaWQiO.."     
}
```

- ### Step 7.1.1) - Client / Resource Server
จัดเก็บ `access_token` + `refresh_token` ใหม่ (set-cookie) รวมทั้ง verify `session_token` ด้วย public key   
และเช็คสิทธิ์ (authorities) ตามข้อมูล `session_token` ที่ระบบ authen (Authorization Server) ส่งมาให้

- ### Step 7.1.1.1) - Client / Resource Server   
ถ้าไม่มีสิทธิ์เข้าถึง จะ return error (`access denied`) กลับไปหา user  

- ### Step 7.1.1.2) - Client / Resource Server  
ถ้ามีสิทธิ์เข้าถึง จะ return resource กลับไปหา user ตามคำร้องที่ browser ส่งมา

- ### Step 7.2) - Authorization Server  
ถ้า `refresh_token` หรือ `user_session` invalid  (ไม่ valid)   
จะ return error กลับไปในรูปแบบ json ([คำอธิบาย error](./../error/))   

> error เหมือนข้อ 4.2 

- ### Step 8) - Client / Resource Server  
redirect ไปที่  Authorization Server `/oauth/authorize` (ต่อจาก 7.2)
