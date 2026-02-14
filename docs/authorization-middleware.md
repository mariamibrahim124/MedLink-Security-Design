# Authorization Middleware Logic

This document describes how protected APIs validate authentication and authorization before accessing the database.

The middleware runs before any secured endpoint (Read / Write operations).

---

## Purpose

The middleware ensures:

- The request contains a valid JWT
- The token signature is valid
- The token is not expired
- The session is still active
- The user has the required role
- The user account is active

---

## Middleware Execution Flow (Pseudo Code)

1. Extract Authorization header from request

2. If header is missing:
      Return 401 Unauthorized

3. Extract JWT token from header

4. Validate token signature using secret key

5. If token invalid or expired:
      Return 401 Unauthorized

6. Extract claims from token:
      - UserId
      - Email
      - Role
      - SessionId (sid)
      - TokenId (jti)

7. Check if session exists in UserSessions table
      WHERE SessionId = sid
      AND IsActive = true
      AND ExpiresAt > current time

8. If session not found:
      Return 401 Unauthorized

9. Check if user exists in Users table
      AND IsActive = true

10. If user not valid:
      Return 401 Unauthorized

11. If endpoint requires specific role:
      Compare token role with required role

12. If role not allowed:
      Return 403 Forbidden

13. Attach user context to request

14. Continue to controller

---

## Security Notes

- All protected endpoints must use this middleware.
- Session validation allows manual token invalidation.
- Logout operation sets IsActive = false in UserSessions table.
- JWT alone is not enough without session validation.
