---
title: "JWT Security Testing Checklist"
date: 2026-07-08
description: "A practical checklist for testing JSON Web Token security in web applications and APIs."
summary: "JWTs are compact signed tokens, but implementation mistakes can turn them into authentication and authorization weaknesses."
tags: ["web security", "jwt", "api security", "authentication", "checklist"]
categories: ["Blog"]
cover:
  image: "cover.png"
  alt: "JSON Web Token security concept with token and lock"
  caption: "JWT security depends on both cryptography and application logic."
  relative: true
draft: false
---

# JWT Security Testing Checklist

JSON Web Tokens (JWTs) are widely used in modern web applications and APIs.

They are compact, portable, and easy to pass between clients and services. They can also become dangerous when teams misunderstand what the token proves, how it should be validated, or where authorization decisions should live.

A JWT is not automatically secure because it looks cryptographic.

The key question is:

> Does the application validate the token correctly and enforce authorization server-side?

Image: Type: Context. Mô tả: "A diagram showing a JWT split into header, payload, and signature, then verified by an API before returning data." caption: "A JWT should be parsed, validated, and mapped to server-side authorization rules."

## Context

A JWT usually has three parts:

```text
header.payload.signature
```

The header describes metadata such as the signing algorithm. The payload contains claims. The signature is used to verify integrity.

Common claims include:

- `sub` - subject or user identifier
- `iss` - issuer
- `aud` - audience
- `exp` - expiration time
- `nbf` - not before
- `iat` - issued at
- `jti` - token identifier
- `role` or custom authorization claims

JWTs are often used for authentication in REST APIs, Single Page Applications, mobile apps, and microservice systems.

## Root Cause

JWT vulnerabilities often come from implementation mistakes rather than JWT itself.

Common root causes:

- The application accepts tokens without verifying the signature.
- The application allows weak or unexpected signing algorithms.
- The secret key is weak, leaked, reused, or hardcoded.
- The application does not validate issuer or audience.
- Expiration is missing or ignored.
- Sensitive authorization claims are trusted without server-side checks.
- Token revocation is not considered.
- Tokens are stored unsafely in the browser.
- Access tokens live too long.

The dangerous assumption is:

> “If the client sends a JWT, the claims inside are true.”

A safer assumption is:

> “Claims are only trustworthy after strict verification and must still be checked against server-side authorization rules.”

## Impact

JWT implementation mistakes can lead to:

- Authentication bypass
- Privilege escalation
- Account takeover
- Access to another user's data
- Long-lived session compromise
- API abuse
- Broken multi-tenant boundaries
- Hard-to-revoke stolen tokens

The impact is high when JWTs are used as the primary security boundary for APIs.

Image: Type: Test case. Mô tả: "A Burp Suite request showing an Authorization: Bearer token header, with the token value partially blurred." caption: "JWT testing starts by identifying where the token is used and what the API trusts."

## JWT Security Testing Checklist

### 1. Identify Where JWTs Are Used

Check common locations:

```http
Authorization: Bearer <token>
Cookie: access_token=<token>
Cookie: session=<token>
```

Also inspect:

- Local storage
- Session storage
- JavaScript variables
- Mobile app traffic
- API documentation
- WebSocket connection parameters

Document whether the JWT is used as an access token, ID token, refresh token, session token, or internal service token.

### 2. Decode the Token Safely

JWT payloads are usually Base64URL-encoded, not encrypted by default.

Decode the token to understand claims, but do not assume the payload is confidential.

Look for:

- User ID
- Email
- Role
- Tenant ID
- Scope
- Expiration
- Issuer
- Audience
- Environment identifiers

If sensitive data is inside a non-encrypted JWT, the issue may be information exposure even if the signature is valid.

### 3. Verify Signature Enforcement

The API must reject modified tokens.

A safe test in a lab or authorized environment:

1. Change a non-sensitive claim in a test token.
2. Send the modified token.
3. Confirm the API rejects it.

Expected behavior: the server returns an authentication failure.

If the server accepts a modified token, signature validation is broken.

Image: Type: PoC. Mô tả: "A lab request where a JWT claim is modified and the API correctly rejects the token. Show expected secure behavior." caption: "Secure APIs reject tampered JWTs."

### 4. Check Algorithm Handling

The server should allow only expected algorithms.

Risk patterns include:

- Accepting unexpected algorithms
- Confusing symmetric and asymmetric verification
- Trusting the token header to decide security policy
- Not pinning the allowed algorithm server-side

The application should not let the client decide how the token should be trusted.

### 5. Check Expiration and Time Claims

Review these claims:

- `exp`
- `nbf`
- `iat`

Testing questions:

- Does the token expire?
- Is the expiration reasonable?
- Does the API reject expired tokens?
- Are long-lived tokens limited to refresh flows?
- Is clock skew handled deliberately?

Access tokens should generally be short-lived. Refresh tokens require stronger storage, rotation, and revocation logic.

### 6. Validate Issuer and Audience

The API should verify that the token was issued by the expected identity provider and intended for the expected application or API.

Check:

- Is `iss` validated?
- Is `aud` validated?
- Can a token for one API be used against another API?
- Can staging tokens work in production?
- Can tokens from another tenant be accepted?

Weak issuer or audience validation is especially dangerous in SSO, OAuth, and multi-tenant systems.

### 7. Test Authorization Claims Carefully

Claims like `role`, `scope`, `tenant`, or `isAdmin` are sensitive.

Testing questions:

- Are roles enforced server-side?
- Can role claims be modified?
- Is tenant isolation enforced beyond the token claim?
- Does the API check object ownership separately?
- Are scopes checked per endpoint?

JWT authentication does not replace object-level authorization.

A valid token for User A should not access User B's objects.

### 8. Check Token Revocation and Logout

JWTs are often stateless, but real applications still need revocation behavior.

Check:

- Does logout invalidate the token?
- Are refresh tokens rotated?
- Can old refresh tokens be reused?
- What happens after password change?
- What happens after account disablement?
- What happens after MFA reset?

If a stolen token remains valid for too long, incident response becomes harder.

### 9. Review Token Storage

Browser storage affects exposure risk.

Common storage locations:

- Cookies
- Local storage
- Session storage
- In-memory app state

Security considerations:

- Cookies should use `HttpOnly`, `Secure`, and appropriate `SameSite` settings.
- Local storage is more exposed to XSS.
- Mobile storage should use platform-protected storage.
- Tokens should not appear in URLs where they can leak through logs or referrers.

### 10. Check Secrets and Key Management

For signed JWTs, key management is critical.

Review:

- Key strength
- Key rotation
- Environment separation
- Secret leakage in repos or logs
- Public key trust model
- JWKS endpoint behavior
- Whether old keys remain valid longer than intended

Cryptography fails when the key lifecycle fails.

## Remediation

Strong JWT security requires:

- Strict signature validation
- Server-side algorithm allowlisting
- Strong secrets or keys
- Issuer and audience validation
- Short-lived access tokens
- Safe refresh token rotation
- Revocation strategy for high-risk events
- Secure token storage
- No sensitive data in plaintext payloads
- Server-side authorization checks per action and object
- Monitoring for token abuse

A good design separates authentication from authorization:

```text
JWT proves identity and claims -> server verifies token -> server checks user permissions and object ownership -> API returns data
```

The JWT should help the server make a decision. It should not replace the decision.

## References

- OWASP WSTG - Testing JSON Web Tokens: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/06-Session_Management_Testing/10-Testing_JSON_Web_Tokens
- OWASP JSON Web Token for Java Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html
- RFC 7519 - JSON Web Token: https://www.rfc-editor.org/rfc/rfc7519

## CTA

KevinSec can help test JWT-based authentication, API authorization, token storage, and session lifecycle controls. If your application relies on Bearer tokens, a focused JWT security review can catch high-impact mistakes early.
