---
title: "API Broken Authentication: Common Testing Patterns"
date: 2026-07-08
description: "A practical checklist for identifying broken authentication patterns in APIs, from token handling to weak login flows and session lifecycle issues."
summary: "API broken authentication appears when an API cannot reliably prove who the caller is or cannot safely manage credentials, tokens, and sessions."
tags: ["api security", "authentication", "broken authentication", "pentest", "owasp api top 10"]
categories: ["Blog"]
cover:
  image: "cover.png"
  alt: "API authentication flow with client token and backend verification"
  caption: "Authentication is the first security decision every API must get right."
  relative: true
draft: false
---

# API Broken Authentication: Common Testing Patterns

API broken authentication happens when an API cannot reliably verify the identity of the caller, or when the surrounding authentication lifecycle is too weak to resist abuse.

For a pentester, the goal is not to "break login" only. The goal is to understand how the API issues, accepts, stores, expires, rotates, revokes, and validates authentication material.

In modern applications, this usually means testing:

- Login flows
- Access tokens
- Refresh tokens
- API keys
- Session cookies
- MFA flows
- Password reset flows
- Mobile API authentication
- SSO/OAuth integrations
- Logout and token revocation

Image: Type: Context. Mô tả: "A diagram showing a client sending credentials to an API, receiving an access token, then using that token to call protected endpoints." caption: "API authentication is more than the login request; it includes the whole token and session lifecycle."

## Context

APIs often rely on tokens instead of traditional server-rendered web sessions.

A simple flow may look like this:

```text
User submits credentials -> API validates credentials -> API returns access token -> client uses token for protected API calls
```

A protected request may look like this:

```http
GET /api/v1/account/profile HTTP/1.1
Host: api.example.com
Authorization: Bearer <access_token>
```

At a minimum, the API needs to answer four questions:

1. Is the token valid?
2. Who does this token represent?
3. Is the token still allowed to be used?
4. Is this caller authorized to perform this specific action?

Broken authentication focuses mainly on the first three questions. Authorization issues such as BOLA focus on the fourth.

## Root Cause

API broken authentication usually comes from weak assumptions.

Common root causes include:

- The API assumes possession of a token is always enough.
- Tokens do not expire, or they expire too slowly.
- Logout does not invalidate tokens.
- Refresh tokens are not rotated or revoked.
- Password reset or MFA flows issue tokens without proper verification.
- API keys are treated like permanent passwords.
- Authentication checks are inconsistent across endpoints.
- Mobile clients are trusted too much.
- Rate limiting is missing from authentication endpoints.
- Error messages expose whether a username, email, or phone number exists.

Authentication is a lifecycle. Weakness in any part of the lifecycle can lead to account takeover, unauthorized API access, or long-lived session abuse.

## Common Testing Patterns

### 1. Check for Missing Authentication

Start by identifying protected endpoints.

Then test whether they still respond when authentication is removed.

Safe testing approach:

```text
Request with valid token -> observe normal response
Request without token -> expect 401 or 403
Request with malformed token -> expect rejection
Request with expired token -> expect rejection
```

The expected result is not always the same status code, but protected data should never be returned.

Risky pattern:

```text
GET /api/v1/users/me -> requires token
GET /api/v1/users/123 -> returns data without token
```

This suggests inconsistent authentication enforcement.

Image: Type: Test case. Mô tả: "A Burp Repeater comparison showing the same API request with Authorization header and without Authorization header, where the unauthenticated request should be rejected." caption: "A protected API endpoint should fail closed when authentication material is missing."

### 2. Test Token Expiration

Tokens should have a defined lifetime.

Check:

- Does the access token expire?
- How long does it remain valid?
- Can an expired token still call protected endpoints?
- Does the server validate expiration or only the client?
- Does the API accept old tokens after password change?

A frequent issue is client-side-only expiration. The frontend stops using the token, but the backend still accepts it.

A secure API must enforce token expiration server-side.

### 3. Test Logout Behavior

Logout should do more than remove a token from browser storage.

Check:

- After logout, can the old access token still call the API?
- Is the refresh token revoked?
- Are server-side sessions invalidated?
- Are all devices logged out when the user requests it?
- Are active sessions visible to the user?

If the API uses stateless JWT access tokens, immediate revocation may require a short token lifetime plus a server-side revocation strategy for high-risk events.

### 4. Test Refresh Token Rotation

Refresh tokens are high-value authentication material.

Review:

- Is the refresh token long-lived?
- Is it rotated after use?
- Can the same refresh token be reused multiple times?
- Is token reuse detected?
- Is the refresh token bound to a device, client, or session?
- Is it revoked after password change or account compromise?

Risky pattern:

```text
Refresh token issued once -> valid for months -> reusable unlimited times
```

A stolen refresh token can become a long-term account access mechanism.

### 5. Test Rate Limiting on Authentication Endpoints

Authentication endpoints should be protected from high-volume abuse.

Review rate limiting on:

- Login
- MFA verification
- OTP verification
- Password reset request
- Password reset token validation
- Email change confirmation
- Phone number verification
- API key creation
- Token refresh

Rate limiting should consider account, IP address, device, session, and risk context.

Do not brute-force real users or production systems. In an authorized pentest, use agreed test accounts and a low-volume test plan approved in the rules of engagement.

### 6. Check Account Enumeration

Authentication flows often leak whether an account exists.

Examples:

```text
"Email does not exist"
"Invalid password"
"This phone number is already registered"
"Reset link sent" only for existing users
Different response time for valid vs invalid accounts
```

Enumeration is not always critical by itself, but it becomes more serious when combined with phishing, credential stuffing, password spraying, or targeted account takeover attempts.

### 7. Review API Key Authentication

API keys are common in developer platforms, SaaS products, and internal services.

Check:

- Are API keys scoped?
- Can users rotate keys?
- Can users revoke keys?
- Are keys shown only once?
- Are keys hashed at rest?
- Are keys logged by the application or reverse proxy?
- Are keys bound to environments?
- Can a read-only key perform write actions?

An API key should be treated like a credential, not like a harmless identifier.

### 8. Test MFA Flow Consistency

MFA can be bypassed when it is applied inconsistently.

Review:

- Does API access require MFA completion?
- Can the user call protected API endpoints before finishing MFA?
- Can an old session skip newly enforced MFA?
- Can password reset disable MFA?
- Can OAuth login bypass MFA?
- Is MFA required for sensitive actions?

A common design flaw is treating MFA as a frontend state instead of a server-side security state.

### 9. Check Authentication State Transitions

High-risk actions should change authentication state.

Examples:

- Password changed
- Email changed
- Phone number changed
- MFA disabled
- Recovery code generated
- New device added
- API key created
- Organization admin added

After these actions, old sessions or tokens may need to be invalidated, reauthenticated, or risk-scored.

### 10. Test Error Handling and Logging

Authentication failures should be logged carefully.

Good logging:

- User identifier or account reference
- Source IP or device context
- Endpoint and event type
- Failed login count
- Token reuse signal
- MFA failure signal

Bad logging:

- Passwords
- Full access tokens
- Full refresh tokens
- Password reset tokens
- MFA codes
- API keys

Sensitive authentication material should never appear in logs.

## Impact

API broken authentication can lead to:

- Account takeover
- Unauthorized API access
- Data exposure
- Session hijacking
- Long-lived token abuse
- Privilege escalation through weak session state
- API key compromise
- Abuse of password reset and MFA flows
- Difficulty investigating incidents due to poor logging

The impact depends on what the authenticated API can access. A weak authentication issue on a profile endpoint may be medium. The same issue on billing, admin, health records, or organization settings can be critical.

## Remediation

A strong API authentication design should include:

- Centralized authentication middleware
- Consistent checks on every protected endpoint
- Short-lived access tokens
- Secure refresh token rotation
- Token revocation for logout and high-risk events
- Strong password reset flow
- MFA for sensitive accounts and actions
- Rate limiting on authentication endpoints
- Generic error messages where appropriate
- Secure token storage guidance for clients
- No sensitive token values in logs
- Monitoring for suspicious authentication events

## Pentester Checklist

Use this checklist during an API pentest:

```text
[ ] Remove Authorization header and confirm protected endpoints reject the request
[ ] Test malformed, expired, and revoked tokens
[ ] Confirm logout invalidates the expected authentication state
[ ] Review refresh token rotation and reuse detection
[ ] Check authentication rate limiting
[ ] Look for account enumeration signals
[ ] Review MFA enforcement consistency
[ ] Test old sessions after password or email change
[ ] Review API key scope, rotation, and revocation
[ ] Confirm sensitive tokens are not logged
```

## References

- OWASP API Security Top 10 2023 - API2 Broken Authentication: https://owasp.org/API-Security/editions/2023/en/0xa2-broken-authentication/
- OWASP WSTG - Authentication Testing: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/04-Authentication_Testing/README
- OWASP Authentication Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
- OWASP ASVS: https://owasp.org/www-project-application-security-verification-standard/

## CTA

KevinSec can help review API authentication flows, token lifecycle controls, MFA enforcement, and session handling. If your product exposes APIs to web, mobile, or third-party clients, a focused authentication review can reduce account takeover risk before attackers find the gaps.
