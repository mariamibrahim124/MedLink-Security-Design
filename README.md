# MedMap Security & Authentication Design
## Overview

This document describes the Authentication and Authorization design for the MedMap backend system.

The system includes:
- Email & Password Authentication
- OTP Email Verification
- JWT-based Authorization
- Session Tracking & Token Invalidation
- Role-based Access Control
## Database Design
 Users Table:
- Id (Guid) - Primary Key
- Email (Unique)
- PasswordHash
- Role
- IsEmailVerified
- CreatedAt
OtpCodes Table:
- Id (Guid)
- UserId (FK)
- Code
- SessionId
- ExpiresAt
- IsUsed
- CreatedAt
  UserSessions Table:
- Id (Guid) - SessionId
- UserId (FK)
- JwtId (jti)
- IsActive
- CreatedAt
- ExpiresAt
  




