# Database Design for Authentication

This document describes the required tables for authentication and session tracking.
Users Table:
-Id (Guid) - Primary Key
-Email (Unique)
-PasswordHash
-Role
-IsEmailVerified
-CreatedAt
OtpCodes Table:
-Id (Guid)
-UserId (FK)
-Code
-SessionId
-ExpiresAt
-IsUsed
-CreatedAt
UserSessions Table:
-Id (Guid) - SessionId
-UserId (FK)
-JwtId (jti)
-IsActive
-CreatedAt
-ExpiresAt


