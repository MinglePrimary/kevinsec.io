---
title: "Path Traversal and LFI: Reading Files You Should Not Read"
date: 2026-07-08
description: "A practical introduction to path traversal and local file inclusion from a web pentesting perspective, with safe testing guidance and remediation patterns."
summary: "Path traversal and LFI happen when applications let untrusted input influence file paths or file inclusion behavior."
tags: ["web security", "path traversal", "lfi", "file inclusion", "beginner"]
categories: ["Blog"]
cover:
  image: "cover.png"
  alt: "File system path security concept with folders and a lock"
  caption: "File paths are security boundaries when user input can influence them."
  relative: true
draft: false
---

# Path Traversal and LFI: Reading Files You Should Not Read

Path traversal and Local File Inclusion (LFI) are classic web security issues, but they still appear in modern applications because file handling is everywhere.

A web application may allow users to download invoices, view profile images, read documents, import templates, render pages, or load language files. These features look harmless. The risk appears when the application uses user-controlled input to decide **which file** should be read, included, or returned.

From a pentester's point of view, this is the key question:

> Can untrusted data influence a server-side file path?

If the answer is yes, the application needs strong validation, canonicalization, and authorization checks.

Image: Type: Context. Mô tả: "A diagram showing a browser sending a request with a file parameter to a web server, and the server reading a file from the filesystem." caption: "Path traversal starts when user input becomes part of a server-side file path."

## Context

A typical file-related endpoint may look like this:

```http
GET /download?file=invoice-2026.pdf HTTP/1.1
Host: example.com
Cookie: session=...
```

The expected behavior is simple: the user downloads a legitimate invoice.

The vulnerable behavior appears when the backend builds a path like this:

```text
/uploads/invoices/ + user_input
```

If `user_input` is trusted too much, an attacker may try to move outside the intended directory and reach files that should never be accessible from the web application.

Path traversal is commonly associated with reading files. LFI is closely related but often involves including or executing a local file through an application-level include mechanism. The impact depends heavily on the technology stack and how the file is processed.

## Root Cause

The root cause is usually one of these mistakes:

- The application concatenates user input directly into a file path.
- The application only checks whether the input looks like a filename, but does not resolve the final path.
- The application uses blocklists instead of allowlists.
- The application does not enforce that the resolved path stays inside the intended base directory.
- The application assumes hidden parameters, database values, or server-side references are always safe.
- The application performs authorization on the page, but not on the file object.

The simplest mental model is:

> A file path is a security decision. Treat it like authorization logic, not string formatting.

## Impact

The impact can range from low to critical depending on what the application can access.

Possible outcomes include:

- Reading application source code
- Reading configuration files
- Exposing credentials or API keys
- Accessing private user files
- Accessing logs
- Disclosing internal paths and framework details
- In some technology stacks, chaining into remote code execution

A finding becomes more severe when the accessed file contains secrets, session material, customer data, cloud credentials, or internal system configuration.

Image: Type: Test case. Mô tả: "A lab screenshot showing a download endpoint where a file parameter is being tested in Burp Suite Repeater. Sensitive values should be blurred." caption: "A safe lab test should prove path control without exposing real sensitive data."

## Signature

A path traversal or LFI candidate often appears in these places:

- `file=`
- `path=`
- `download=`
- `template=`
- `page=`
- `view=`
- `lang=`
- `theme=`
- `document=`
- `image=`
- `attachment=`

Example request patterns:

```http
GET /download?file=report.pdf HTTP/1.1
GET /image?name=avatar.png HTTP/1.1
GET /render?template=invoice.html HTTP/1.1
GET /docs/view?path=user-guide.pdf HTTP/1.1
```

The interesting part is not only the parameter name. The real signal is whether the response suggests that the backend is reading from the filesystem.

Look for:

- File content returned directly
- Different errors for existing vs non-existing files
- Server-side stack traces mentioning paths
- Response headers such as `Content-Disposition`
- File metadata in the response
- Different response size when changing the filename

## How to Test Safely

Test only in systems where you have authorization, such as PortSwigger Web Security Academy, DVWA, OWASP Juice Shop, or an approved pentest scope.

A safe testing workflow:

1. Identify endpoints that read or download files.
2. Confirm the normal behavior with a legitimate file.
3. Change the filename to another allowed file and observe the response.
4. Test whether the application accepts path-like input.
5. Check whether the final file is restricted to the intended directory.
6. Avoid accessing real secrets or production-sensitive files unless your rules of engagement explicitly allow it.
7. Document the minimum proof needed to show the control failure.

A good pentest report does not need to dump sensitive files. It needs to prove that access control on file paths is broken.

Image: Type: PoC. Mô tả: "A PortSwigger path traversal lab with Burp Suite showing the vulnerable request and the successful lab completion banner. Do not include sensitive real-world files." caption: "Use intentionally vulnerable labs to demonstrate the pattern safely."

## Common Mistakes in Fixes

### 1. Removing Only One Pattern

Some fixes only remove one traversal pattern. This is fragile because path handling can vary by operating system, framework, encoding, and proxy layer.

A stronger fix is to resolve the final path and verify that it remains inside an approved base directory.

### 2. Trusting File Extensions

Checking that a filename ends in `.pdf` or `.jpg` is not sufficient. Extension validation does not prove that the final resolved path is safe, nor does it prove that the current user is allowed to access that file.

### 3. Using Blocklists

Blocklists are easy to bypass and hard to maintain. Use an allowlist of known file IDs, database references, or server-generated object identifiers instead.

### 4. Forgetting Authorization

Even if path traversal is fixed, the application may still allow one user to download another user's file by changing an object reference.

That becomes an access control issue, often related to IDOR or BOLA.

## Remediation

Strong defenses include:

- Use server-side file IDs instead of user-controlled file paths.
- Store file metadata in a database and map IDs to server-controlled paths.
- Enforce authorization on every file access.
- Normalize and canonicalize paths before access.
- Verify that the resolved path stays inside the intended directory.
- Avoid passing user input into file include functions.
- Use allowlists for file types and storage locations.
- Store uploaded files outside the web root when possible.
- Return generic errors that do not leak internal paths.
- Monitor abnormal file access patterns.

A safer design is:

```text
User requests: /download?id=12345
Server checks: Does this user own file 12345?
Server resolves: Internal trusted file path from database
Server returns: File only if authorization succeeds
```

This separates user input from filesystem control.

## Pentester Notes

When testing path traversal and LFI, focus on the data flow:

```text
request parameter -> backend variable -> path construction -> file read/include -> response
```

If you can influence the path, ask three questions:

1. Can I move outside the intended directory?
2. Can I access a file that belongs to another user?
3. Can the file content expose secrets or enable a higher-impact chain?

The best findings are not just “path traversal exists.” The best findings explain what security boundary failed and what business impact follows.

## References

- OWASP WSTG - Testing Directory Traversal File Include: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/01-Testing_Directory_Traversal_File_Include
- OWASP WSTG - Testing for File Inclusion: https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_File_Inclusion
- PortSwigger Web Security Academy - Path Traversal: https://portswigger.net/web-security/file-path-traversal

## CTA

KevinSec helps teams review file handling, upload/download workflows, and access control logic before attackers find weak paths. If your application serves user files, documents, reports, or media, consider a focused web security review.
