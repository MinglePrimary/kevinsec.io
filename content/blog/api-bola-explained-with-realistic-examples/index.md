---
title: "API BOLA Explained with Realistic Examples"
date: 2026-07-08
description: "A practical explanation of Broken Object Level Authorization in APIs, with realistic examples, testing mindset, and remediation guidance."
summary: "BOLA happens when an API exposes object identifiers but fails to verify whether the caller is allowed to access the object."
tags: ["api security", "bola", "idor", "access control", "owasp api top 10"]
categories: ["Blog"]
cover:
  image: "cover.png"
  alt: "API object authorization concept with user, object ID, and lock"
  caption: "Every object access needs an authorization decision."
  relative: true
draft: false
---

# API BOLA Explained with Realistic Examples

Broken Object Level Authorization (BOLA) is one of the most important API security issues.

It happens when an API exposes an object identifier and fails to verify whether the current user is allowed to access that object.

The vulnerable pattern looks simple:

```text
User A requests object ID 1001 -> API returns object 1001
User A changes object ID to 1002 -> API returns object 1002 even though it belongs to User B
```

This is the API version of a very common access control failure. In older web security language, it is often discussed as IDOR. In modern API security, BOLA is the broader and more API-focused category.

Image: Type: Context. Mô tả: "A diagram showing User A sending an API request for object 1001, then changing the object ID to 1002, with the API missing an authorization check." caption: "BOLA appears when object identifiers are checked for existence, but not ownership or permission."

## Context

APIs expose object identifiers everywhere:

- User IDs
- Order IDs
- Invoice IDs
- Ticket IDs
- Project IDs
- Organization IDs
- File IDs
- Message IDs
- Payment method IDs
- Workspace IDs
- Tenant IDs

Example API request:

```http
GET /api/v1/invoices/1001 HTTP/1.1
Host: api.example.com
Authorization: Bearer <token>
```

The API authenticates the user, finds invoice `1001`, and returns it.

The missing question is:

> Does this authenticated user have permission to access invoice 1001?

Authentication answers “who are you?”

Object-level authorization answers “are you allowed to access this specific object?”

BOLA happens when the second question is skipped.

## Root Cause

BOLA usually appears because the backend checks only part of the security logic.

Common root causes:

- The API verifies that the user is logged in but not that the object belongs to the user.
- The API trusts object IDs from the client.
- Authorization is enforced in the frontend but not the backend.
- Developers assume UUIDs are unguessable and therefore safe.
- The API uses direct database lookups by ID without scoping the query to the current user or tenant.
- Different endpoints use inconsistent authorization middleware.
- Internal APIs become exposed without adding object-level checks.

A vulnerable backend query may conceptually behave like this:

```text
Find invoice where id = requested_id
```

A safer query behaves like this:

```text
Find invoice where id = requested_id AND owner = current_user
```

In multi-tenant systems, the authorization boundary may be user, organization, role, project, or workspace.

## Impact

BOLA can expose or modify data across users and tenants.

Possible impact includes:

- Reading another user's profile
- Downloading another user's invoice
- Viewing another customer's support ticket
- Editing another user's address
- Canceling another user's order
- Accessing another organization's project
- Reading private files
- Changing another user's settings
- Performing actions across tenant boundaries

BOLA impact increases when the affected object contains personal data, financial data, business data, authentication settings, or privileged actions.

Image: Type: Test case. Mô tả: "A lab API test using two test accounts. Account A requests its own object, then tries Account B's object ID. Show the expected access denied result or vulnerable result in a lab." caption: "BOLA testing requires at least two users or roles to validate object-level authorization."

## Signature

Look for API requests that include object identifiers.

Common patterns:

```http
GET /api/users/123
GET /api/orders/98765
GET /api/invoices/2026-001
GET /api/projects/abc123/files/file789
PUT /api/profile/address/555
DELETE /api/messages/9001
POST /api/workspaces/77/invite
```

Identifiers may appear in:

- URL path
- Query string
- Request body
- Headers
- GraphQL variables
- JSON arrays
- Batch operations
- WebSocket messages

Do not ignore UUIDs. BOLA is not about whether the ID is easy to guess. BOLA is about whether access to that object is authorized.

## Realistic Examples

### Example 1: Invoice Download

User A downloads an invoice:

```http
GET /api/invoices/inv_1001
Authorization: Bearer token_user_a
```

Then User A changes the invoice ID to another value discovered from an email, URL, receipt pattern, or browser history.

The API should return `403 Forbidden` or `404 Not Found` if User A does not own that invoice.

If the API returns another user's invoice, this is BOLA.

### Example 2: Support Ticket Access

A SaaS application exposes:

```http
GET /api/tickets/8842
```

If users from one organization can access tickets from another organization by changing the ticket ID, the issue becomes cross-tenant data exposure.

This is often high severity in B2B applications.

### Example 3: Updating Another User's Resource

BOLA is not only about reading data.

Example:

```http
PUT /api/users/123/address
```

If User A can update User B's address by changing the ID, the impact becomes unauthorized state change.

State-changing BOLA can be more severe than read-only exposure.

### Example 4: Nested Object Confusion

Nested routes can hide authorization mistakes:

```http
GET /api/projects/project-a/files/file-b
```

The API may check access to `project-a`, but forget to verify that `file-b` actually belongs to that project.

A secure implementation must check the relationship between parent and child objects.

### Example 5: Batch Operations

Batch APIs are common:

```json
{
  "file_ids": ["file_1", "file_2", "file_3"]
}
```

The API must authorize every object in the list, not just the request itself.

Image: Type: PoC. Mô tả: "A controlled API lab showing two accounts and an object ID swap test. Use a mock API, PortSwigger API labs, or OWASP Juice Shop where applicable." caption: "Object ID swap testing is the basic BOLA validation pattern."

## How to Test Safely

Use authorized systems and test accounts only.

A safe BOLA testing workflow:

1. Create or obtain two test users.
2. Generate objects under User A and User B.
3. Capture User A's valid API request.
4. Replace the object ID with User B's object ID.
5. Send the request using User A's token.
6. Confirm whether the API enforces object-level authorization.
7. Repeat across read, update, delete, and action endpoints.
8. Document the smallest safe proof.

Expected secure behavior:

- `403 Forbidden` when the object exists but access is denied
- `404 Not Found` when the application intentionally hides object existence
- No sensitive data returned
- No state change performed

Avoid accessing real customer data. Use your own test objects whenever possible.

## BOLA vs IDOR

IDOR usually describes a direct object reference that can be manipulated.

BOLA is broader and more API-specific. It focuses on whether object-level authorization is enforced for every object access.

In practice:

```text
IDOR is a common pattern.
BOLA is the authorization failure category.
```

A UUID-based API can still have BOLA. A GraphQL API can still have BOLA. A batch endpoint can still have BOLA. The identifier format does not fix authorization.

## Remediation

Strong BOLA prevention requires server-side authorization for every object access.

Recommended controls:

- Enforce object-level authorization in the backend.
- Scope database queries by current user, organization, tenant, or role.
- Do not rely on frontend checks.
- Do not rely on unguessable IDs as the primary control.
- Centralize authorization logic where possible.
- Check parent-child object relationships.
- Authorize every object in batch operations.
- Use deny-by-default authorization.
- Add automated tests for cross-user and cross-tenant access.
- Log and alert on suspicious object access failures.

Safer query pattern:

```text
SELECT invoice WHERE id = requested_id AND tenant_id = current_tenant AND user_has_permission = true
```

The exact implementation depends on your stack, but the principle is universal:

> Never return or modify an object before verifying that the caller is allowed to access that specific object.

## Pentester Notes

The strongest BOLA reports include:

- Two test accounts
- The original authorized request
- The modified object ID request
- Evidence of unauthorized data access or state change
- Clear business impact
- Exact affected endpoint
- Recommended authorization fix

Do not stop after finding one endpoint. BOLA often appears as a pattern across a resource family.

If `/api/invoices/{id}` is vulnerable, check related endpoints such as download, update, export, share, delete, and batch operations.

## References

- OWASP API Security Top 10 2023 - API1: Broken Object Level Authorization: https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/
- OWASP API Security Project: https://owasp.org/www-project-api-security/
- PortSwigger Web Security Academy - API Testing: https://portswigger.net/web-security/api-testing

## CTA

KevinSec helps teams test API authorization, IDOR/BOLA patterns, and multi-tenant access control. If your API exposes user, invoice, project, or organization IDs, object-level authorization should be tested before release.
