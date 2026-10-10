**Table of Contents**

* [Authentication Strategies](#authentication-strategies)
* [Authentication VS Authorization](#authentication-vs-authorization)
* [Basic Authentication](#basic-authentication)
* [Session Based Authentication](#session-based-authentication)
* [Token Based Authentication](#token-based-authentication)
* * [JSON Web Tokens](#json-web-tokens-jwt)
* [Open Authorization](#open-authorization)
* [Single Sign-on](#single-sign-on)

---

# Authentication Strategies

Authentication strategies verify a user's identity to grant access. Common methods include `Basic Auth` (username/password), `Session-based` (server remembers login), `Token-based` (like JWT, a secure digital key), `OAuth` (for third-party access like "Login with Google"), and `SSO` (Single Sign-On, one login for many apps). The appropriate strategy depends on security requirements, application architecture, and user experience.

# Authentication VS Authorization

**Authentication** verifies who a user is, while **authorization** determines what that user is allowed to access or do.

Example:
* **Authentication:** John successfully logs in using his credentials.
* **Authorization:** John cannot delete other users because his role is `user`, not `admin`.

# Basic Authentication

Basic Authentication is an HTTP authentication method that sends a **username** and **password** **encoded in Base64** in the Authorization header. **Base64 is not encryption, so HTTPS is essential.** It is generally unsuitable for modern user-facing applications without additional security measures.

```
GET /api/profile HTTP/1.1
Authorization: Basic dXNlcjpwYXNzd29yZA==
```

![basic-authentication-image](./assets/basic-authentication.png)

# Session Based Authentication

Session-Based Authentication stores authentication state on the server. **After a successful login, the server creates a session and sends the client a session ID, usually in a cookie.** The browser includes the cookie in subsequent requests, allowing the server to identify the user.

```typescript
// Express.js example using express-session
app.post("/login", (req, res) => {
  req.session.userId = "123";
  res.send("Logged in");
});
```

![session-based-authentication-image](./assets/session-authentication.png)

* **Questions:**
* * Can't I just send my `session-id` to someone else?

# Token Based Authentication

Token-Based Authentication uses a token issued after successful authentication. **The client sends the token with subsequent requests, and the server validates it to determine whether the request is authorized.** Tokens may be opaque or self-contained, and their security depends on storage, expiration, and validation.

```
GET /api/profile HTTP/1.1
Authorization: Bearer your-access-token
```

![token-based-authentication-image](./assets/token-authentication.png)

- Session Based Auth is stateful, but Token Based is stateless. The server doesnt store it, only validates it.

* **Questions:**
* * Whats the main difference between Token based and Session Based Authentication?

## JSON Web Tokens (JWT)

JSON Web Tokens (JWTs) are a token format used to securely transmit claims between parties. **A JWT typically contains a header, a payload, and a cryptographic signature.** The signature allows the recipient to verify integrity and authenticity, but the payload is usually readable and is not encrypted by default.

```typescript
import jwt from "jsonwebtoken";

const token = jwt.sign(
  { userId: "123" },
  process.env.JWT_SECRET!,
  { expiresIn: "15m" }
);
```

![json-web-tokens-image](./assets/jwt-authentication.png)

# Open Authorization

OAuth is an authorization framework that allows an application to access specific resources on behalf of a user without requiring the application to receive the user's password. For user sign-in, OAuth is commonly combined with OpenID Connect (OIDC), which adds an identity layer.

**Example:** A user authorizes a photo-printing application to access selected photos from a cloud storage provider without sharing their cloud account password.

![open-authorization-image](./assets/open-authorization.png)

# Single Sign-on

Single Sign-On (SSO) allows users to authenticate once with an identity provider and then access multiple connected applications without signing in separately to each one. It commonly uses standards such as OpenID Connect or SAML.

**Example:** An employee signs in through their company's identity provider and can then access the company dashboard, email, and project management tools without separate logins.

![single-sign-on-image](./assets/single-sign-on.png)