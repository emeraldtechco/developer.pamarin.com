# การเข้าสู่ระบบ (Authentication)

# บทความ
- [แนวทางปฏิบัติที่ดี ในการทำ OAuth 2.0 Access Token & Refresh Token เพื่อความปลอดภัย](https://medium.com/@jittagornp/best-practice-%E0%B9%83%E0%B8%99%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B8%97%E0%B8%B3-oauth2-access-token-refresh-token-457ae3bee4b7)

# Flow
- [Authorization Code](#authorizationcode)

# Authorization Code
![authentication-flow.svg](./authentication-flow.svg)

# Step

2. นำ `access_token` มา build http post 
ส่งไปตรวจสอบที่ `authen.pamarin/com/oauth/session`
```
Request Method : POST
Content-Type : application/x-www-form-urlencoded
Authorization : Bearer $ACCESS_TOKEN  
```
4.1 ถ้า `access_token` valid
จะ return `user_session` กลับมาให้ในรูปแบบ json
```json
TODO
```
