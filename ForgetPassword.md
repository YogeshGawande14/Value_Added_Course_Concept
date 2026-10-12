# Forgot Password Flow — Spring Boot Backend

A beginner-friendly guide to implementing a secure **Forgot Password / Reset Password** feature using Java, Spring Boot, a database, email, and BCrypt.

This is a separate Markdown file. Save it as `FORGOT_PASSWORD_README.md` or `forgot-password.md` in your GitHub repository.

---

## 1. What Does Forgot Password Do?

When a user forgets their password, they can prove control of their registered email address and choose a new password.

They do **not** need to know their old password.

### Example

- Registered email: `yogesh@example.com`
- User clicks **Forgot Password**
- Backend sends a password-reset link to that email
- User opens the link and enters a new password
- Backend verifies the reset token
- Backend hashes the new password using BCrypt and updates the database

**Important:** Never email the old password. A secure system cannot recover a password from its BCrypt hash.

---

## 2. Complete Flow at a Glance

```text
Frontend: Forgot Password page
            |
            | POST /api/auth/forgot-password
            | { "email": "yogesh@example.com" }
            v
AuthController
            |
            v
AuthService
            |
            | Validate email input
            | Look up account
            | Create secure random reset token
            | Store token hash + expiry
            v
Database (user + password reset token record)
            |
            v
Email Service sends reset link
            |
            v
User opens reset link
            |
            | Enters new password
            | POST /api/auth/reset-password
            | { "token": "...", "newPassword": "..." }
            v
AuthController
            |
            v
AuthService validates token
            |
            | Token exists?
            | Token expired?
            | Token already used?
            v
Hash new password using BCrypt
            |
            v
Update user password in database
            |
            v
Mark reset token as used / invalidate it
            |
            v
Return success response
```

---

## 3. Step 1 — User Clicks “Forgot Password”

The login page contains a link such as:

```text
Forgot Password?
```

When the user clicks it, the frontend displays a form asking for the registered email address.

Example input:

```text
Email: yogesh@example.com
[Send reset link]
```

At this point, the user has **not** proved that they own the email. The backend must not change the password yet.

### Why do we ask for an email?

The backend needs to identify which account should receive the reset link.

---

## 4. Step 2 — Frontend Sends an API Request

The frontend sends an HTTP POST request.

### Endpoint

```http
POST /api/auth/forgot-password
Content-Type: application/json
```

### Request body

```json
{
  "email": "yogesh@example.com"
}
```

### What is an API request?

An API request is a message sent by the frontend to ask the backend to perform an operation.

- `POST` sends data to the backend.
- `/api/auth/forgot-password` identifies the endpoint.
- JSON carries the email in the request body.

Do not send the new password at this stage. The user has not opened the reset link yet.

---

## 5. Step 3 — Controller Receives the Request

The controller handles HTTP requests and passes the work to the service layer.

Example structure:

```java
@RestController
@RequestMapping("/api/auth")
public class AuthController {

    private final AuthService authService;

    public AuthController(AuthService authService) {
        this.authService = authService;
    }

    @PostMapping("/forgot-password")
    public ResponseEntity<String> forgotPassword(
            @RequestBody ForgotPasswordRequest request) {

        authService.forgotPassword(request.getEmail());

        return ResponseEntity.ok(
                "If the account exists, a reset link will be sent."
        );
    }
}
```

This is an illustrative example. The request DTO and service method must also be created.

### Why use a controller?

The controller:
1. Receives the HTTP request.
2. Reads the request data.
3. Calls the service.
4. Returns an HTTP response.

Keep the main business logic out of the controller.

---

## 6. Step 4 — Validate the Email

The service first checks that the supplied email is present and has a valid format.

Example checks:

- Is the email empty?
- Is the format valid?
- Does the account exist?

The backend may look up the user using a repository method such as:

```java
Optional<User> findByEmail(String email);
```

### Privacy rule: Do not reveal whether an account exists

A safe response is:

```text
If the account exists, a reset link will be sent.
```

Use the same public response whether the email exists or not. This makes it harder for attackers to discover registered email addresses.

Apply rate limits and monitoring to reduce automated abuse.

---

## 7. Step 5 — Generate a Secure Reset Token

If the account exists, the backend generates a **cryptographically secure, random, unpredictable token**.

A reset token is different from a password hash and a JWT.

| Item | Purpose |
|---|---|
| Password hash | Protects the stored password |
| Reset token | Proves possession of a valid reset link |
| JWT | Carries signed claims for authentication |
| Session ID | Identifies a server-side session |

A reset token should be:
- Random and difficult to guess.
- Short-lived, for example 15–30 minutes according to the application's policy.
- Valid for one successful use only.
- Bound to the intended account and reset operation.

### Store the token safely

A recommended design stores a **hash of the reset token**, not the raw token, in the database. The raw token is placed in the emailed reset link. When the user submits it, the backend hashes the submitted token and compares it with the stored hash.

Use a cryptographic hash such as SHA-256 for a high-entropy random reset token. Passwords require a password-hashing algorithm such as BCrypt because human-chosen passwords have much less entropy.

---

## 8. Step 6 — Save the Reset Token Record

A dedicated table can store reset-token information.

Example table: `password_reset_tokens`

| Column | Purpose |
|---|---|
| `id` | Unique record ID |
| `user_id` | Account that requested the reset |
| `token_hash` | Hash of the raw reset token |
| `expires_at` | Date/time when the token expires |
| `used_at` | When the token was consumed; null while unused |
| `created_at` | When the record was created |

Example conceptual record:

```text
user_id:    1
token_hash: 9f86d081...
expires_at: 2026-10-12 15:45:00
used_at:    null
```

The values above are illustrative, not a real token.

### Why store an expiry time?

Without expiry, a leaked reset link might remain usable indefinitely.

### Why store `used_at`?

It helps enforce single-use behaviour. A successfully used token must not reset the password again.

---

## 9. Step 7 — Send the Reset Link by Email

The backend uses an email service to send a link to the user's registered email address.

Example link:

```text
https://your-frontend.example/reset-password?token=RAW_RANDOM_TOKEN
```

The frontend URL is illustrative; replace it with your real application URL.

### Email flow

```text
AuthService
    |
    v
EmailService
    |
    v
Email provider / SMTP server
    |
    v
User's registered email inbox
```

The link contains the raw reset token because the user needs it to prove possession of the link. The database should contain only the token hash.

### Email security

- Use HTTPS for the reset page.
- Never put the user's password in the email.
- Do not log the raw reset token.
- Avoid third-party analytics or external resources on the reset page that could leak URL tokens.
- Consider putting the token in the URL fragment or using a short-lived, carefully designed exchange flow if URL leakage is a concern.
- Do not mark the token as used just because the link was opened. Consume it when a valid reset succeeds.

If email delivery fails, handle the error without exposing account existence. Design token cleanup/retry behaviour carefully.

---

## 10. Step 8 — User Opens the Link and Enters a New Password

The reset page shows a form:

```text
New password:        ********
Confirm new password: ********

[Reset Password]
```

The frontend should check that both password fields match for a good user experience.

**The backend must validate the password too.** Frontend validation alone is not security.

The backend should enforce the application's password policy, reject invalid input, and never return the submitted password in its response.

---

## 11. Step 9 — Frontend Calls the Reset API

### Endpoint

```http
POST /api/auth/reset-password
Content-Type: application/json
```

### Request body

```json
{
  "token": "RAW_RANDOM_TOKEN",
  "newPassword": "MyNewStrongPassword123!"
}
```

This is a sample request. Do not use the example password in a real account.

The reset token is required because the user may not be logged in. The backend uses it to authorize this specific password-reset operation.

---

## 12. Step 10 — Validate the Reset Token

Before changing the password, the service checks all of the following:

1. Was a token supplied?
2. Does its hash match a stored reset-token record?
3. Does the token belong to an existing user?
4. Has the token expired?
5. Has it already been used or revoked?
6. Does the account still permit password reset?

If any check fails, reject the reset request.

### Token decision

```text
Token received
     |
     v
Hash submitted token
     |
     v
Find matching token-hash record
     |
     v
Record found?
  /       \
 No       Yes
 |          |
Reject   Check expiry
            |
            v
       Already used?
        /       \
      Yes       No
       |         |
     Reject   Allow reset
```

The service must also protect against two simultaneous requests consuming the same token. Use a transaction and appropriate database locking or an atomic conditional update.

---

## 13. Step 11 — Hash the New Password Using BCrypt

After the token is validated and the new password passes validation, hash the password.

Example Spring Security code:

```java
String passwordHash = passwordEncoder.encode(newPassword);
```

A typical configuration can use Spring Security's `BCryptPasswordEncoder`.

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

The exact configuration depends on your Spring Boot and Spring Security setup.

### What happens?

```text
New password
     |
     v
BCryptPasswordEncoder
     |
     v
Password hash
     |
     v
Save hash in users.password
```

Do not encrypt a password in a reversible way or save it as plain text. During normal login, use:

```java
passwordEncoder.matches(enteredPassword, storedPasswordHash);
```

Do not compare a newly encoded BCrypt string directly with the old hash: BCrypt uses a salt, so repeated encodings of the same password usually differ.

---

## 14. Step 12 — Update the Database and Consume the Token

After all validation succeeds, the backend should perform the password update and token consumption safely, ideally in one database transaction.

The transaction should:

1. Update the user's password hash.
2. Mark the reset token as used or revoke it.
3. Invalidate other outstanding reset tokens for that user, according to policy.
4. Optionally revoke existing refresh tokens or sessions.
5. Commit the transaction.

If the update fails, the transaction should roll back rather than leave a partially completed reset.

### Before

```text
users.password = old_BCrypt_hash
reset_token.used_at = null
```

### After

```text
users.password = new_BCrypt_hash
reset_token.used_at = current_time
```

The original password is never retrieved or needed.

---

## 15. Step 13 — Return a Response

Successful reset:

```http
200 OK
```

Example:

```json
{
  "message": "Password has been reset successfully."
}
```

For an invalid, expired, or used token, return a suitable error without exposing internal details. A generic response such as `400 Bad Request` can be used for an invalid reset request.

After a successful reset, direct the user to log in with the new password. Do not automatically log them in unless that behaviour has been intentionally designed and secured.

---

## 16. What Happens to Existing JWTs and Sessions?

Changing a password does not automatically invalidate every already-issued JWT unless the application has a revocation strategy.

Possible approaches:

- Revoke refresh tokens for the account.
- Invalidate server-side sessions.
- Use short-lived access tokens.
- Maintain a token version or account security timestamp and check it during authentication.
- Require reauthentication for sensitive operations.

Choose the approach that matches the application's security design. For a session-based application, invalidate relevant sessions after a password reset. For JWT authentication, plan explicitly for token revocation.

---

## 17. Suggested Spring Boot Package Structure

```text
src/main/java/com/example/app/
|
├── controller/
│   └── AuthController.java
|
├── service/
│   ├── AuthService.java
│   ├── PasswordResetService.java
│   └── EmailService.java
|
├── repository/
│   ├── UserRepository.java
│   └── PasswordResetTokenRepository.java
|
├── entity/
│   ├── User.java
│   └── PasswordResetToken.java
|
├── dto/
│   ├── ForgotPasswordRequest.java
│   └── ResetPasswordRequest.java
|
└── security/
    └── SecurityConfig.java
```

### Responsibility of each file

| File | Work |
|---|---|
| `AuthController` | Receives forgot/reset HTTP requests |
| `AuthService` | Coordinates authentication-related operations |
| `PasswordResetService` | Generates and validates reset tokens, updates password |
| `EmailService` | Sends reset email |
| `UserRepository` | Finds and updates users |
| `PasswordResetTokenRepository` | Saves and finds reset-token records |
| `User` | Maps the user table |
| `PasswordResetToken` | Maps reset-token information |
| `ForgotPasswordRequest` | Holds the submitted email |
| `ResetPasswordRequest` | Holds the reset token and new password |
| `SecurityConfig` | Configures public and protected endpoints |

The forgot/reset endpoints generally need to be reachable without an existing login, but the reset token itself must be validated before allowing a password change.

---

## 18. API Summary

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/api/auth/forgot-password` | Request a reset link |
| `POST` | `/api/auth/reset-password` | Reset password using a valid token |
| `POST` | `/api/auth/login` | Log in with the new password |

---

## 19. Security Checklist

- [ ] Return a generic response for existing and non-existing email addresses.
- [ ] Rate-limit reset requests and token-validation attempts.
- [ ] Generate cryptographically secure random tokens.
- [ ] Store only a hash of each reset token.
- [ ] Give tokens a short expiry time.
- [ ] Allow a token to succeed only once.
- [ ] Validate the new password on the backend.
- [ ] Store passwords using BCrypt or another suitable password-hashing algorithm.
- [ ] Never log raw tokens or passwords.
- [ ] Use HTTPS and a trusted email delivery method.
- [ ] Perform password update and token consumption atomically.
- [ ] Invalidate outstanding reset tokens after a successful reset.
- [ ] Decide how to revoke sessions and refresh tokens.
- [ ] Notify the user that their password was changed, without including the password.

---

## 20. Quick Revision

**Forgot Password:**

```text
Enter email
    -> Validate request
    -> Return generic response
    -> Generate random reset token
    -> Store token hash and expiry
    -> Email reset link
    -> User enters new password
    -> Validate token
    -> Validate password
    -> Hash password using BCrypt
    -> Update password and consume token
    -> Revoke relevant tokens/sessions
    -> Return success
```

### Remember these three points

1. **Reset token** proves possession of a valid reset link.
2. **BCrypt** protects the newly stored password.
3. **JWT/session revocation** is a separate step; changing the password does not automatically invalidate every existing access token.

This README explains the design and request flow. Before production use, implement and test token generation, expiry, single-use enforcement, email delivery, transaction handling, rate limiting, and session/token revocation.
