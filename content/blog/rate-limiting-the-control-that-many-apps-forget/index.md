---
title: "Rate Limiting: The Control That Many Apps Forget"
date: 2026-07-08
description: "A practical guide to rate limiting as a security control for login, OTP, password reset, API, and resource-intensive endpoints."
summary: "Rate limiting is not only a performance control. It is a security boundary for authentication, abuse prevention, and API resource protection."
tags: ["web security", "api security", "rate limiting", "authentication", "abuse prevention"]
categories: ["Blog"]
cover:
  image: "cover.png"
  alt: "API request traffic passing through a rate limiting control"
  caption: "Without rate limiting, small features can become abuse channels."
  relative: true
draft: false
---

# Rate Limiting: The Control That Many Apps Forget

Rate limiting is one of the most practical security controls in web applications and APIs.

It is also one of the controls teams forget until something goes wrong.

A login endpoint works. OTP works. Password reset works. Search works. File upload works. The API returns data correctly. But no one asks:

> How many times can a client repeat this action?

That question is where rate limiting starts.

Image: Type: Context. Mô tả: "A diagram showing many client requests entering an API gateway, with a rate limiter allowing normal traffic and slowing abusive traffic." caption: "Rate limiting protects business logic, infrastructure, and users."

## Context

Rate limiting controls how often a client can perform an action within a defined time window.

It can apply to:

- IP address
- User account
- Session
- API key
- Device fingerprint
- Tenant
- Endpoint
- Business action

Security-sensitive actions include:

- Login attempts
- Password reset requests
- OTP validation
- MFA challenge creation
- Email verification
- Username availability checks
- Coupon redemption
- Search and export features
- File upload
- Expensive API queries
- AI or third-party integration calls

Rate limiting is not just about stopping denial-of-service attacks. It also prevents brute force, enumeration, automation abuse, and cost amplification.

## Root Cause

Rate limiting failures usually happen because applications are built around happy paths.

Developers test:

```text
Can the user log in?
Can the user request an OTP?
Can the user search data?
Can the user call the API?
```

Pentesters ask:

```text
Can the user repeat the action 1,000 times?
Can the action be automated?
Can the limit be bypassed by changing IP, account, session, or header?
Can the feature create cost or load for the business?
```

Common root causes:

- No limit exists.
- The limit exists only per IP address.
- The limit resets too quickly.
- The limit can be bypassed by changing headers.
- Different endpoints share the same sensitive action but not the same limit.
- The application limits requests but not expensive operations.
- There is no monitoring for abuse patterns.

## Impact

Missing rate limiting can lead to:

- Credential stuffing
- Password brute force
- OTP brute force
- Account enumeration
- Email or SMS bombing
- Coupon abuse
- Resource exhaustion
- API scraping
- Increased cloud or third-party service costs
- Denial of service
- Abuse of AI or compute-heavy features

In OWASP API Security Top 10 2023, resource consumption is treated as a major API security risk because satisfying API requests consumes CPU, memory, bandwidth, storage, and sometimes paid third-party services.

Image: Type: Test case. Mô tả: "A lab chart showing repeated requests to an OTP verification endpoint and the expected secure response after a limit is reached." caption: "Security-sensitive actions should become harder to repeat, not easier to automate."

## Signature

Look for endpoints where repetition changes risk:

```http
POST /login
POST /api/auth/login
POST /forgot-password
POST /reset-password/request
POST /otp/verify
POST /mfa/challenge
POST /email/verify
GET /api/search?q=...
POST /api/export
POST /api/upload
POST /api/invite
POST /api/coupon/apply
```

Potential signs of weak rate limiting:

- No delay after many failed attempts
- Same response speed after repeated failures
- No lockout or progressive throttling
- Limit only applies to one endpoint variant
- Limit can be bypassed by changing IP-like headers
- Limit does not apply across accounts or sessions
- No user notification for suspicious attempts
- No audit trail

## How to Test Safely

Rate limit testing must be controlled. Do not stress production systems or send high-volume traffic unless explicitly authorized.

A safe workflow:

1. Confirm the endpoint and action sensitivity.
2. Use a test account.
3. Send a small, controlled number of repeated requests.
4. Observe whether throttling, lockout, or delay appears.
5. Test whether the limit is per account, per IP, per session, or per action.
6. Stop before causing service degradation.
7. Document the minimum evidence required.

For public blog content, use labs, staging environments, or local applications to demonstrate the concept.

Image: Type: PoC. Mô tả: "A Burp Suite Intruder or Repeater-based lab test showing a low-volume sequence of failed OTP attempts and the absence or presence of throttling." caption: "Rate limit testing should be controlled and low volume unless explicitly authorized."

## Where Rate Limiting Matters Most

### 1. Login

Login endpoints need protection against brute force and credential stuffing.

Recommended controls:

- Per-account throttling
- Per-IP throttling
- Progressive delays
- Suspicious login detection
- MFA for risky events
- Credential stuffing monitoring

### 2. OTP and MFA

OTP verification is especially sensitive because codes are short by design.

Controls should include:

- Strict attempt limits per code
- Expiration
- Single-use verification
- Per-account and per-session limits
- Resend limits
- Notification on suspicious activity

### 3. Password Reset

Password reset can be abused for account enumeration, email flooding, or token brute force.

Controls should include:

- Generic responses
- Request throttling
- Per-account cooldowns
- Token attempt limits
- Monitoring for repeated recovery attempts

### 4. Search and Export

Search and export endpoints can be abused for data scraping or resource exhaustion.

Controls should include:

- Pagination limits
- Query cost limits
- Export size limits
- Per-user quotas
- Async job limits
- Monitoring for unusual patterns

### 5. Paid or Third-Party Integrations

Endpoints that trigger SMS, email, AI inference, payment checks, or external API calls can generate real costs.

Controls should include:

- Quotas
- Budget thresholds
- Tenant-level limits
- Abuse alerts
- Circuit breakers

## Common Bypass Patterns

A rate limit is weak if it only checks one signal.

Common bypass ideas to defend against:

- Rotating IP addresses
- Changing sessions
- Changing accounts
- Changing case or encoding
- Switching endpoint versions
- Using mobile vs web API variants
- Abusing batch endpoints
- Adding misleading forwarding headers

The fix is not to block every trick individually. The fix is to model the business action and enforce limits at the right layer.

## Remediation

Strong rate limiting should be layered:

- Per IP
- Per account
- Per session
- Per API key
- Per tenant
- Per endpoint
- Per business action
- Per resource cost

Additional controls:

- Progressive throttling
- Temporary lockout for high-risk actions
- CAPTCHA only where appropriate
- Monitoring and alerting
- Abuse analytics
- Clear user messaging
- Safe defaults at API gateway and application layers

A practical policy example:

```text
OTP verification: 5 attempts per code, 10 attempts per account per hour, resend cooldown, alert on suspicious attempts
```

The exact numbers depend on the business, but the principle is fixed: repeated sensitive actions must have boundaries.

## References

- OWASP API Security Top 10 2023 - API4: Unrestricted Resource Consumption: https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/
- OWASP Authentication Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
- OWASP Forgot Password Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Forgot_Password_Cheat_Sheet.html

## CTA

KevinSec can help test login, OTP, password reset, API, and resource-heavy features for abuse resistance. If a feature can be repeated, it needs a security-focused rate limit review.
