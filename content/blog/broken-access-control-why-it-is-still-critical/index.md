---
title: "Broken Access Control: Why It Is Still Critical"
date: 2026-07-08T00:00:00+07:00
draft: false
description: "Understand why broken access control remains one of the most critical web application security risks."
summary: "Broken access control allows authenticated users to access data or actions they should not be allowed to access."
categories: ["Blog"]
tags: ["broken access control", "IDOR", "authorization", "web security", "OWASP"]
author: "KevinSec"
showToc: true
TocOpen: false
cover:
  image: "cover.png"
  alt: "Broken access control in web applications"
  caption: ""
  relative: true
---

# Broken Access Control: Why It Is Still Critical

Broken Access Control is one of the most important vulnerability categories in web application security. It happens when an application fails to enforce what an authenticated user is allowed to access or modify.

This issue is dangerous because it often appears in normal business features, not only in obviously technical endpoints. A user profile page, invoice download, project dashboard, order history, admin panel, or API endpoint can all become vulnerable if authorization checks are incomplete.

The core idea is simple:

> Authentication proves who the user is. Authorization decides what the user is allowed to do.

When applications confuse these two concepts, broken access control appears.

Image: Type: Context, Mô tả: "A simple diagram showing User A trying to access User B's invoice through an object ID, with a missing authorization check highlighted.". caption: "Broken access control occurs when the server fails to verify whether the current user is allowed to access the requested object or action."

## Authentication Is Not Enough

Many applications correctly require login but fail to enforce proper authorization after login.

Example:

```text
GET /api/invoices/7788
Authorization: Bearer token-for-user-A
```

If invoice `7788` belongs to User B, the server must reject the request. It is not enough to check that the token is valid. The server must verify that User A is allowed to access invoice `7788`.

This is why broken access control can be subtle. The application may appear secure because unauthenticated users are blocked. But authenticated users may still access other users' data.

## Common Broken Access Control Patterns

### 1. IDOR

Insecure Direct Object Reference occurs when a user can change an object identifier and access another user's object.

Examples:

```text
/api/users/1001
/api/orders/7788
/download?file_id=9001
```

If changing the ID returns another user's data, the root cause is missing object-level authorization.

### 2. Horizontal Privilege Escalation

Horizontal privilege escalation happens when one normal user accesses another normal user's data or actions.

Example:

- User A views User B's profile details
- User A downloads User B's invoice
- User A updates User B's address

Both users may have the same role, but they should not access each other's resources.

### 3. Vertical Privilege Escalation

Vertical privilege escalation happens when a lower-privileged user performs higher-privileged actions.

Example:

```text
POST /admin/users/disable
```

If a normal user can call this endpoint successfully, the system has a serious authorization flaw.

### 4. Missing Function-Level Authorization

Sometimes the UI hides admin features, but the backend does not enforce authorization.

This is a common mistake:

- The admin button is hidden from normal users
- The normal user manually sends the admin request
- The backend accepts the action

Security must be enforced server-side. Hiding UI elements is not access control.

### 5. Forced Browsing

Forced browsing happens when users access URLs or endpoints directly without going through the intended workflow.

Examples:

```text
/admin
/export/all-users.csv
/internal/reports/monthly
```

If these paths are accessible without proper authorization, sensitive data or functionality may be exposed.

## Why Broken Access Control Is Still Common

### Authorization Logic Is Business-Specific

Unlike input validation or password hashing, authorization rules are often unique to each business.

For example:

- Who can approve a payment?
- Who can view a project?
- Can a manager view all team members?
- Can support staff reset customer data?
- Can users access invoices after leaving an organization?

These rules are difficult for automated scanners to understand.

### Modern Applications Have Many Roles and Objects

Applications now include:

- Users
- Organizations
- Teams
- Projects
- Workspaces
- API keys
- Invoices
- Reports
- Admin functions
- Shared resources

Each object may require different authorization logic.

### APIs Expose More Direct Access

APIs often expose object IDs directly:

```text
GET /api/projects/123
PATCH /api/users/456
DELETE /api/files/789
```

This creates a large attack surface for object-level access control issues.

### Frontend Authorization Is Misunderstood

Modern single-page applications often contain frontend route guards or role checks. These checks improve user experience but do not replace backend enforcement.

A user can still call the backend directly using tools such as Burp Suite, curl, or browser dev tools.

## Impact of Broken Access Control

The impact depends on the object and action involved.

Possible impact includes:

- Personal data exposure
- Account takeover
- Unauthorized financial transactions
- Privilege escalation
- Data modification or deletion
- Admin function abuse
- Compliance violations
- Loss of customer trust

Broken access control often has high business impact because it directly affects confidentiality, integrity, and sometimes availability.

## How to Test Safely

Use authorized test accounts with different roles.

Example setup:

| Account | Role | Purpose |
|---|---|---|
| User A | Normal user | Owns object A |
| User B | Normal user | Owns object B |
| Manager | Elevated user | Limited admin functions |
| Admin | Administrator | Full access |

Testing flow:

1. Log in as User A.
2. Capture a request for User A's object.
3. Log in as User B and identify User B's object ID.
4. As User A, attempt to access User B's object in the authorized lab or assessment scope.
5. Verify whether the server rejects the request.

Image: Type: Test case, Mô tả: "A Burp Suite Repeater screenshot in a lab showing two requests with different object IDs and different authorization contexts.". caption: "Access control testing requires at least two accounts to verify object ownership and role boundaries."

## Testing Checklist

Review these areas:

- Can User A access User B's object?
- Can a normal user call admin endpoints directly?
- Can object IDs be changed in paths, query strings, headers, or JSON bodies?
- Are authorization checks enforced server-side?
- Are roles checked for every sensitive action?
- Does the API verify object ownership?
- Can users access deleted, archived, or inactive resources?
- Can users modify fields they should not control?
- Are export/download endpoints protected?
- Are batch operations properly authorized per object?

## Common Remediation Strategies

### Enforce Server-Side Authorization

Every sensitive request must be authorized on the server.

Do not rely on:

- Hidden buttons
- Frontend routes
- Disabled form fields
- Client-side role checks
- Obscure URLs

### Deny by Default

The safe default is to deny access unless explicitly allowed.

### Check Object Ownership

For object-based access, verify that the current user has permission to access the specific object.

Example logic:

```text
current_user must be owner of invoice OR have organization-level permission to view invoices
```

### Centralize Authorization Logic

Authorization logic should not be randomly duplicated across controllers. Use centralized policies, middleware, guards, or permission services where appropriate.

### Log Sensitive Authorization Failures

Failed access attempts to sensitive resources should be logged and reviewed.

## How to Explain It to Developers

A clear explanation helps remediation.

Weak report:

> I changed the ID and saw another user's data.

Better report:

> The endpoint validates authentication but does not enforce object-level authorization. An authenticated user can change the invoice ID in the request path and retrieve invoices belonging to other users. The server should verify that the requested invoice belongs to the current user or that the user has an authorized role to access it.

This explains root cause and remediation clearly.

## Conclusion

Broken Access Control remains critical because it is deeply connected to business logic. It is not just a technical mistake. It is a failure to enforce who can access what, under which conditions, and for which actions.

For pentesters, this category should be tested carefully with multiple accounts and clear authorization boundaries. For developers, authorization must be treated as a server-side security requirement, not a UI feature.

## References

- OWASP Top 10:2025 - Broken Access Control: https://owasp.org/Top10/2025/en/
- OWASP Web Security Testing Guide - Authorization Testing: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/README
- OWASP API Security Top 10 2023 - BOLA: https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/

## Need Help?

KevinSec provides practical web and API security testing with a strong focus on access control, authorization logic, and business impact. If you need to validate whether your application enforces access control correctly, contact KevinSec through the Contact page.
