# Backend Authentication & Authorization

Beginner-friendly notes on how authentication works in a backend application, especially with Java and Spring Boot.

## 1. Complete Backend Flow

```text
Registration
    |
    v
Validate Input
    |
    v
Check if Email Already Exists
    |
    v
Hash Password using BCrypt
    |
    v
Save User in Database
    |
    v
Login
    |
    v
Verify Email and Password
    |
    v
Create JWT Token or Session
    |
    v
Client Sends Protected API Request
    |
    v
Authenticate Request
    |
    v
Check Authorization / Permissions
    |
    v
Controller -> Service -> Repository -> Database
    |
    v
Return Response
```

## 2. Registration Flow

Example endpoint:

```http
POST /api/auth/register
```

Example request body:

```json
{
  "name": "Yogesh",
  "email": "yogesh@example.com",
  "password": "myPassword123"
}
```

Backend steps:

1. `AuthController` receives the request.
2. `AuthService` validates the input.
3. The service checks whether the email is already registered.
4. BCrypt hashes the password.
5. `UserRepository` saves the user in the database.
6. The API returns a success response.

**Important:** Store the password hash, never the original password.

## 3. Login and Verification Flow

Example endpoint:

```http
POST /api/auth/login
```

Backend steps:

1. Receive the email and password.
2. Find the user by email.
3. Use BCrypt to compare the entered password with the stored hash.
4. Check account status and any required email/OTP verification.
5. If valid, create a JWT token or a server-side session.
6. If invalid, return an appropriate error response.

Email verification is a separate step when the application requires proof that the user owns the email address.

## 4. Authentication vs Authorization

| Concept | Meaning | Example |
|---|---|---|
| Authentication | Verifies who the user is | Login with email and password |
| Authorization | Checks what the user can access | Only an ADMIN can delete users |
| Verification | Checks a required condition | Confirm an email or OTP |

**Easy memory trick:**
- Authentication = Who are you?
- Authorization = What are you allowed to do?

## 5. Hashing vs `hashCode()` vs JWT

### Password Hashing — BCrypt

- Converts a password into a one-way password hash.
- During login, BCrypt checks the entered password against the stored hash.
- A password hash is not meant to be decoded back into the original password.
- BCrypt is designed for password storage.

### Java `hashCode()`

- A method inherited from Java's `Object` class.
- Returns an integer hash value.
- Used by collections such as `HashMap` and `HashSet`.
- **It is not a password-security method.**

### JWT — JSON Web Token

- A compact token containing claims, such as a user ID and expiry time.
- It is digitally signed so the backend can validate its integrity.
- It is commonly used to carry authentication information between requests.
- A normal signed JWT is not automatically encrypted. Do not put passwords or secrets in its payload.

## 6. JWT API Request Flow

```text
1. Client sends login request
             |
             v
2. Backend verifies credentials
             |
             v
3. Backend creates a signed JWT
             |
             v
4. Client stores the token securely
             |
             v
5. Client calls a protected API
             |
             v
6. Client sends the token in the request header
             |
             v
7. JWT filter validates the token
             |
             v
8. Spring Security identifies the user
             |
             v
9. Backend checks the user's permissions
             |
             v
10. Controller processes the request
```

Example request header:

```http
Authorization: Bearer <your-jwt-token>
```

JWTs should have an expiry time. Logout may require clearing the client token and, depending on the design, revoking refresh tokens or maintaining a token denylist.

## 7. JWT vs Server-Side Session

| Feature | JWT | Server-side Session |
|---|---|---|
| Login state | Usually carried in the token | Stored on the server |
| Client sends | JWT with each protected request | Session cookie |
| Server storage | Can be stateless | Stores session data |
| Logout | May require token/refresh-token revocation | Server can invalidate the session |
| Common use | APIs and distributed systems | Traditional web applications |

A session ID is an identifier used to look up session data. It is not the user's password.

## 8. Suggested Spring Boot Package Structure

```text
src/main/java/com/example/app/
|
├── controller/
│   └── AuthController.java
|
├── service/
│   └── AuthService.java
|
├── repository/
│   └── UserRepository.java
|
├── entity/
│   └── User.java
|
├── dto/
│   ├── RegisterRequest.java
│   └── LoginRequest.java
|
├── security/
│   ├── SecurityConfig.java
│   └── JwtAuthenticationFilter.java
|
└── util/
    └── JwtService.java
```

| File / Layer | Responsibility |
|---|---|
| `AuthController` | Receives API requests and returns responses |
| `AuthService` | Handles registration, login and verification logic |
| `UserRepository` | Reads and writes user records |
| `User` | Represents the user database table |
| DTO classes | Define request/response data |
| `SecurityConfig` | Configures authentication and access rules |
| `JwtAuthenticationFilter` | Validates bearer tokens on protected requests |
| `JwtService` | Creates and validates JWTs |

## 9. Common HTTP Status Codes

| Status | Meaning |
|---|---|
| `200 OK` | Request successful |
| `201 Created` | Resource created |
| `400 Bad Request` | Invalid input |
| `401 Unauthorized` | Missing or invalid authentication |
| `403 Forbidden` | Authenticated, but access is denied |
| `409 Conflict` | Duplicate email or conflicting data |
| `500 Internal Server Error` | Unexpected server error |

## 10. Security Checklist

- [ ] Validate all incoming data.
- [ ] Hash passwords using BCrypt or another suitable password-hashing algorithm.
- [ ] Never store plain-text passwords.
- [ ] Keep JWT signing keys outside source code.
- [ ] Use HTTPS.
- [ ] Set suitable token expiry times.
- [ ] Apply rate limits to login and verification endpoints.
- [ ] Check authorization on every protected operation.
- [ ] Avoid exposing sensitive account information in error messages.
- [ ] Store tokens securely and plan logout/revocation behaviour.

## 11. Quick Revision

1. **Registration:** Validate -> Hash -> Save.
2. **Login:** Find user -> Verify password -> Create JWT/session.
3. **Authentication:** Verify the user's identity.
4. **Authorization:** Check permissions.
5. **BCrypt:** Safely hashes passwords and checks password matches.
6. **`hashCode()`:** Supports Java hash-based collections; not password security.
7. **JWT:** Carries signed claims between client and backend.
8. **Session:** Keeps login state on the server.
9. **API flow:** Request -> Security checks -> Controller -> Service -> Repository -> Database -> Response.

### One-Line Flow

```text
Register -> Validate -> Hash -> Save -> Login -> Verify
-> JWT/Session -> Authenticate Request -> Authorize -> Execute API
```
