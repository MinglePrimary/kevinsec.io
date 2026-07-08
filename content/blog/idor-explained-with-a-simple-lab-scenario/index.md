---
title: "IDOR Explained with a Simple Lab Scenario"
date: 2026-07-08T00:00:00+07:00
draft: false
description: "A beginner-friendly explanation of IDOR using a safe lab scenario."
summary: "Learn how Insecure Direct Object Reference works, why it happens, and how to test it safely in labs such as PortSwigger, DVWA, or OWASP Juice Shop."
categories: ["Blog"]
tags: ["IDOR", "broken access control", "web pentest", "lab", "beginner"]
author: "KevinSec"
showToc: true
TocOpen: false
cover:
  image: "cover.png"
  alt: "IDOR lab scenario explained"
  caption: ""
  relative: true
---

# IDOR Explained with a Simple Lab Scenario

IDOR stands for **Insecure Direct Object Reference**. It is a common access control vulnerability where an application exposes a direct reference to an internal object and fails to verify whether the current user is allowed to access that object.

In simple terms:

> If changing an ID in a request lets you access another user's data, you may be looking at IDOR.

IDOR is part of the broader Broken Access Control category. It is especially common in APIs because object IDs often appear directly in paths, query parameters, or request bodies.

Image: Type: Context, Mô tả: "A simple diagram showing User A requesting /api/invoices/1001 and then changing the ID to /api/invoices/1002, which belongs to User B.". caption: "IDOR happens when the server trusts the object ID but does not verify whether the current user is authorized to access it."

## The Core Concept

Consider this request:

```http
GET /api/invoices/1001 HTTP/1.1
Host: lab.example
Authorization: Bearer token-for-user-a
```

If invoice `1001` belongs to User A, the request is legitimate.

Now consider this modified request:

```http
GET /api/invoices/1002 HTTP/1.1
Host: lab.example
Authorization: Bearer token-for-user-a
```

If invoice `1002` belongs to User B, the server should reject the request.

If the server returns User B's invoice to User A, the application is vulnerable to IDOR.

## Root Cause

The root cause is missing object-level authorization.

The application checks:

```text
Is the user logged in?
```

But fails to check:

```text
Is this user allowed to access this specific object?
```

Authentication is not enough. The server must enforce authorization for every object and action.

## A Simple Lab Scenario

Use only legal labs or authorized test environments such as PortSwigger Web Security Academy, DVWA, or OWASP Juice Shop.

Imagine a training application with two accounts:

| Account | Role | Object owned |
|---|---|---|
| Alice | Normal user | Invoice `1001` |
| Bob | Normal user | Invoice `1002` |

Alice logs in and views her invoice:

```http
GET /account/invoices/1001 HTTP/1.1
Host: vulnerable-lab.local
Cookie: session=alice-session
```

The application returns Alice's invoice.

The tester then changes the object ID:

```http
GET /account/invoices/1002 HTTP/1.1
Host: vulnerable-lab.local
Cookie: session=alice-session
```

Expected secure behavior:

```http
HTTP/1.1 403 Forbidden
```

Vulnerable behavior:

```http
HTTP/1.1 200 OK

Invoice owner: Bob
Amount: $500
```

This is IDOR because Alice can access Bob's object by modifying a direct object reference.

Image: Type: Test case, Mô tả: "A PortSwigger-style lab screenshot or Burp Suite Repeater screenshot showing an object ID changed from one authorized object to another test user's object.". caption: "A safe IDOR test compares access between two authorized lab accounts."

## Where IDOR Appears

IDOR can appear in many places.

### URL Paths

```text
/users/1024
/orders/7788
/files/9001
```

### Query Parameters

```text
/download?file_id=9001
/profile?user_id=1024
/report?id=7788
```

### JSON Bodies

```json
{
  "userId": 1024,
  "invoiceId": 7788
}
```

### HTTP Headers

```http
X-User-ID: 1024
X-Account-ID: 7788
```

### GraphQL Variables

```json
{
  "id": "VXNlcjoxMDI0"
}
```

The object reference does not have to be a simple number. It can be a UUID, hash, base64 value, slug, file name, or encoded global ID.

## Numeric IDs vs UUIDs

Some teams believe that replacing numeric IDs with UUIDs fixes IDOR.

UUIDs can reduce guessability, but they do not replace authorization.

If a user obtains another user's UUID through search results, logs, shared links, browser history, API responses, or leaked data, the same issue may still exist.

The real fix is server-side authorization.

## IDOR Impact

Impact depends on the object and action.

Read-only IDOR may expose:

- Personal data
- Invoices
- Documents
- Support tickets
- Private messages
- API keys
- Internal reports

Write-based IDOR may allow:

- Updating another user's profile
- Deleting another user's files
- Changing shipping addresses
- Modifying project settings
- Canceling orders
- Approving actions

Write-based IDOR is often more severe because it affects integrity, not only confidentiality.

## How to Test IDOR Safely

A proper test requires at least two accounts.

### Step 1: Create or obtain two test accounts

Use accounts that are explicitly authorized for testing.

```text
User A: alice@example.test
User B: bob@example.test
```

### Step 2: Capture User A's request

Use Burp Suite Proxy or browser dev tools.

Example:

```text
GET /api/documents/501
```

### Step 3: Identify User B's equivalent object

Log in as User B and find a similar request:

```text
GET /api/documents/702
```

### Step 4: Replay User A's request with User B's object ID

While authenticated as User A, change `501` to `702`.

### Step 5: Evaluate the response

Secure outcomes:

- `403 Forbidden`
- `404 Not Found`
- Empty result
- Generic error without data exposure

Vulnerable outcome:

- User B's object is returned
- User B's object is modified
- The action succeeds without authorization

## What to Include in a Report

A good IDOR report should include:

- Affected endpoint
- Roles/accounts used
- Original authorized request
- Modified unauthorized request
- Evidence of unauthorized access
- Business impact
- Clear remediation

Example root cause statement:

```text
The endpoint validates that the requester is authenticated but does not verify that the requested invoice belongs to the authenticated user. As a result, an authenticated user can modify the invoice ID and retrieve invoices belonging to other users.
```

## Remediation

### Enforce Object-Level Authorization

For every object access, the server should verify ownership or permission.

Example:

```text
invoice.owner_id == current_user.id
```

or:

```text
current_user has organization permission: invoice:read
```

### Avoid Trusting Client-Supplied Ownership

Do not trust values like:

```json
{
  "ownerId": 123,
  "userId": 456
}
```

The server should derive ownership from the authenticated session and database records.

### Apply Authorization to Every Action

Authorization must be enforced for:

- Read
- Create
- Update
- Delete
- Export
- Share
- Approve
- Batch operations

### Use Centralized Authorization Policies

Centralized policies reduce inconsistent authorization logic across endpoints.

## Common Mistakes

Avoid these assumptions:

- "The endpoint requires login, so it is safe."
- "The ID is a UUID, so users cannot guess it."
- "The frontend hides the button, so users cannot access it."
- "Only mobile apps use this API, so users cannot modify requests."
- "The object ID is encoded, so it is protected."

These assumptions fail because clients are under user control.

## Conclusion

IDOR is simple to understand but still very common. The vulnerability appears when an application exposes object references and fails to enforce object-level authorization.

For pentesters, the key is to test with multiple authorized accounts and compare access boundaries. For developers, the key is to enforce authorization on the server for every object and action.

## References

- OWASP API Security Top 10 2023 - Broken Object Level Authorization: https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/
- OWASP Web Security Testing Guide - Authorization Testing: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/README
- PortSwigger Web Security Academy - Access Control: https://portswigger.net/web-security/access-control

## Need Help?

KevinSec helps teams test object-level authorization across web applications and APIs. If you want to validate whether your application is vulnerable to IDOR or BOLA, contact KevinSec through the Contact page.
