# Database Design for Authentication

This document describes the required tables for authentication and session tracking.

---

## Users Table

- Id (GUID) – Primary Key  
- Email (Unique)  
- PasswordHash  
- Role  
- IsEmailVerified  
- CreatedAt  

---

## OtpCodes Table

- Id (GUID) – Primary Key  
- UserId (FK)  
- Code  
- SessionId  
- ExpiresAt  
- IsUsed  
- CreatedAt  

---

## UserSessions Table

- Id (GUID) – Primary Key  
- UserId (FK)  
- JwtId (jti)  
- SessionId (sid)  
- IsActive  
- ExpiresAt  
- CreatedAt  
