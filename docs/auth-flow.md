# Authentication Flow Design

This document describes the authentication process including:

- Signup
- Email Verification (OTP)
- Login
- Logout
- Request New OTP

---

## 1️⃣ Signup Process

### Description

The user registers using email and password.  
Email must be verified before full authentication.

### Pseudo Code

 FUNCTION Signup(email, password):

    existingUser = Users.FindByEmail(email)

    IF existingUser != null THEN
        RETURN Error("Email already exists")

    hashedPassword = Hash(password)

    newUser = Create User:
        Email = email
        PasswordHash = hashedPassword
        IsEmailVerified = false
        Role = "User"

    Users.Insert(newUser)

    otpCode = GenerateSecure6DigitCode()
    otpSessionId = GenerateGuid()
    expirationTime = Now + 5 minutes

    otpRecord = Create OtpCode:
        UserId = newUser.Id
        Code = otpCode
        SessionId = otpSessionId
        ExpiresAt = expirationTime
        IsUsed = false

    OtpCodes.Insert(otpRecord)

    SendEmail(email, otpCode)

    RETURN Success(otpSessionId)


---

## 2️⃣ Verify OTP Process

### Description

User submits OTP to verify email and receive JWT token.

### Pseudo Code

FUNCTION VerifyOtp(sessionId, code):

    otpRecord = OtpCodes.Find(
        SessionId = sessionId AND
        Code = code AND
        IsUsed = false AND
        ExpiresAt > Now
    )

    IF otpRecord == null THEN
        RETURN Error("Invalid or expired OTP")

    otpRecord.IsUsed = true
    OtpCodes.Update(otpRecord)

    user = Users.FindById(otpRecord.UserId)
    user.IsEmailVerified = true
    Users.Update(user)

    sessionId = GenerateGuid()
    jwtId = GenerateGuid()
    tokenExpiration = Now + TokenLifetime

    session = Create UserSession:
        Id = sessionId
        UserId = user.Id
        JwtId = jwtId
        IsActive = true
        ExpiresAt = tokenExpiration

    UserSessions.Insert(session)

    token = GenerateJwt({
        sub: user.Id,
        email: user.Email,
        role: user.Role,
        jti: jwtId,
        sid: sessionId,
        exp: tokenExpiration
    })

    RETURN Success(token)

---

## 3️⃣ Login Process

### Description

User logs in using verified email and password.

### Pseudo Code

FUNCTION Login(email, password):

    user = Users.FindByEmail(email)

    IF user == null THEN
        RETURN Error("Invalid credentials")

    IF !VerifyPassword(password, user.PasswordHash) THEN
        RETURN Error("Invalid credentials")

    IF user.IsEmailVerified == false THEN
        RETURN Error("Email not verified")

    sessionId = GenerateGuid()
    jwtId = GenerateGuid()
    tokenExpiration = Now + TokenLifetime

    session = Create UserSession:
        Id = sessionId
        UserId = user.Id
        JwtId = jwtId
        IsActive = true
        ExpiresAt = tokenExpiration

    UserSessions.Insert(session)

    token = GenerateJwt({
        sub: user.Id,
        email: user.Email,
        role: user.Role,
        jti: jwtId,
        sid: sessionId,
        exp: tokenExpiration
    })

    RETURN Success(token)


---

## 4️⃣ Logout Process

### Description

Invalidates the active session and token.

### Pseudo Code

FUNCTION Logout(currentToken):

    sessionId = ExtractClaim(currentToken, "sid")

    session = UserSessions.FindById(sessionId)

    IF session == null THEN
        RETURN Error("Session not found")

    session.IsActive = false
    UserSessions.Update(session)

    RETURN Success("Logged out")

---

## 5️⃣ Request New OTP

### Description

Generates a new OTP if the previous one expired.

### Pseudo Code

FUNCTION RequestNewOtp(email):

    user = Users.FindByEmail(email)

    IF user == null THEN
        RETURN Error("User not found")

    otpCode = GenerateSecure6DigitCode()
    otpSessionId = GenerateGuid()
    expirationTime = Now + 5 minutes

    otpRecord = Create OtpCode:
        UserId = user.Id
        Code = otpCode
        SessionId = otpSessionId
        ExpiresAt = expirationTime
        IsUsed = false

    OtpCodes.Insert(otpRecord)

    SendEmail(email, otpCode)

    RETURN Success(otpSessionId)

