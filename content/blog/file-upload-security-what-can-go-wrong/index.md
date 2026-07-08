---
title: "File Upload Security: What Can Go Wrong?"
date: 2026-07-08
description: "A practical overview of file upload vulnerabilities, common failure patterns, testing ideas, and secure design recommendations."
summary: "File upload features are high-risk because they accept complex user-controlled content and often interact with storage, parsing, and public access paths."
tags: ["web security", "file upload", "upload security", "application security", "intermediate"]
categories: ["Blog"]
cover:
  image: "cover.png"
  alt: "File upload security concept with document and warning icon"
  caption: "File upload features accept complex untrusted content and need multiple layers of defense."
  relative: true
draft: false
---

# File Upload Security: What Can Go Wrong?

File upload looks like a simple feature. A user selects a file, clicks upload, and the application stores it.

From a security perspective, file upload is much more complex. The application receives user-controlled content, metadata, file names, file types, file sizes, and sometimes archives or images that must be parsed by backend services.

That makes file upload one of the most important features to test carefully.

Image: Type: Context. Mô tả: "A diagram showing user uploads file, application validates it, stores it, scans it, and serves it back through a download endpoint or CDN." caption: "A secure upload flow includes validation, storage isolation, scanning, and safe retrieval."

## Context

Common upload features include:

- Profile avatar upload
- Resume upload
- Document submission
- Support ticket attachments
- Product image upload
- Chat file sharing
- Import CSV or spreadsheet
- Admin media library
- PDF invoice upload

Each of these features has different business rules. A profile avatar should not accept the same file types as a document upload portal. A CSV import feature has different risks from an image upload feature.

The first question is not "Can I upload a payload?"

The first question is:

> What file types does the business process actually need?

## Root Cause

File upload vulnerabilities usually happen because the application trusts one or more user-controlled properties:

- File extension
- Content-Type header
- File name
- File size
- File content
- Image metadata
- Archive structure
- Storage path
- Client-side validation
- Public accessibility

The root cause is insufficient validation and unsafe handling of untrusted files.

## Common Failure Patterns

### 1. Trusting Content-Type

The `Content-Type` header is controlled by the client. If the server accepts files only because the request says `image/jpeg`, the control is weak.

The server should inspect the file content and enforce business-specific allowlists.

Image: Type: PoC. Mô tả: "Burp Suite Repeater showing an upload request where Content-Type is changed in a lab environment." caption: "Content-Type is useful metadata, not a security boundary."

### 2. Extension Blacklist Bypass

Some applications block dangerous extensions using a blacklist.

Blacklists are fragile because attackers may find alternate extensions, case variations, double extensions, or server-specific execution behavior.

A stronger approach is to use an allowlist of permitted extensions based on business need.

### 3. File Name Injection and Path Traversal

If the application uses the original file name directly, a malicious file name may cause problems.

Risks include:

- Path traversal
- File overwrite
- Log injection
- Header injection in downloads
- Unsafe rendering in HTML
- Confusing file names for users

The application should generate server-side file names and store the original name only as metadata after safe normalization.

### 4. Publicly Executable Upload Directory

A high-risk pattern is storing uploaded files in a web-accessible directory where the server may execute scripts.

Even if the upload feature is intended for images, a misconfiguration may allow server-side code execution if dangerous files are accepted and executed.

Uploaded files should be stored outside the web root or served through a controlled download handler.

### 5. Missing Size Limits

Large files can cause denial-of-service risk by consuming disk, memory, bandwidth, or processing time.

The application should enforce file size limits at multiple layers:

- Reverse proxy
- Application server
- Upload handler
- Storage layer
- Background processing pipeline

### 6. Unsafe Image or Document Processing

Many applications process uploaded files: resizing images, extracting metadata, parsing PDFs, or importing spreadsheets.

Parsers can have vulnerabilities. Even when the upload validation is correct, the processing pipeline may be exposed to malicious files.

Security controls should include sandboxing, patching, and scanning where appropriate.

### 7. Archive Extraction Risks

Archive uploads introduce additional risks:

- Zip slip/path traversal during extraction
- Compression bombs
- Nested archive bombs
- Unexpected file types inside archive
- Overwrite of existing files

If archive upload is not required, do not allow it. If it is required, extract in a sandboxed environment and enforce strict limits.

### 8. Insecure File Retrieval

Upload security is not only about accepting files. Retrieval matters too.

Questions to ask:

- Can users access files uploaded by other users?
- Are file IDs protected by authorization checks?
- Are direct object references exposed?
- Are private files stored under public URLs?
- Are download links long-lived?
- Are file names safely encoded in responses?

A secure upload feature can still become a broken access control issue if retrieval is weak.

## Impact

File upload vulnerabilities can lead to:

- Stored XSS
- Malware hosting
- Sensitive file exposure
- Account takeover through uploaded HTML/SVG in some contexts
- Denial of service
- File overwrite
- Server-side request or parser exploitation
- Remote code execution in severe misconfigurations
- Unauthorized access to private documents

Impact depends heavily on file type, storage path, server configuration, and who can access the uploaded file.

## Signature

Signals that may indicate upload risk:

- Client-side-only validation
- Content-Type trusted as the main control
- Dangerous extensions accepted
- Files stored under predictable public paths
- Original file names used directly
- Uploaded files served with unsafe content types
- Private files accessible without authorization
- No file size limit
- No antivirus or malware scanning where required
- Image/document processing performed without isolation
- Archive extraction supported without strict controls

## How to Test Safely

Only test file upload features in authorized scope or labs.

A safe workflow:

1. Identify allowed file types and business purpose.
2. Test allowed files first.
3. Check server-side validation, not only client-side validation.
4. Inspect upload request in Burp Suite.
5. Verify whether file extension, content type, and content are validated.
6. Check where the file is stored and how it is served.
7. Test whether another user can access the uploaded file.
8. Test file size and rate limits carefully.
9. Avoid uploading malware or destructive files.
10. Use lab environments for high-risk scenarios.

Image: Type: Test case. Mô tả: "A file upload testing checklist showing extension validation, MIME validation, file content validation, storage isolation, and access control." caption: "Upload testing requires multiple control checks."

Recommended labs:

- PortSwigger file upload labs
- DVWA file upload module
- OWASP Juice Shop upload-related challenges

## Remediation

A secure upload design should include:

- Business-specific file type allowlist
- Server-side validation
- File signature or content validation where appropriate
- Safe server-generated file names
- Storage outside web root
- No script execution in upload directories
- Safe download handler with authorization checks
- Strict file size limits
- Malware scanning where required
- Image/document processing isolation
- Archive extraction sandboxing
- Safe response headers for downloads
- Logging and monitoring for abnormal upload behavior

For image uploads, consider re-encoding images server-side to remove dangerous metadata and normalize the file.

For private files, never rely only on unguessable URLs. Enforce authorization.

## References

- [PortSwigger Web Security Academy: File Upload Vulnerabilities](https://portswigger.net/web-security/file-upload)
- [PortSwigger Lab: Web shell upload via Content-Type restriction bypass](https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-content-type-restriction-bypass)
- [OWASP File Upload Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html)
- [OWASP WSTG: Testing for File Upload](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Business_Logic_Testing/08-Test_Upload_of_Unexpected_File_Types)

## Final Thoughts

File upload is a trust boundary.

Every uploaded file is untrusted until the application validates it, stores it safely, processes it safely, and serves it safely.

A mature security review should test the whole lifecycle: upload, validation, storage, processing, access control, and download.

---

**Need help reviewing file upload and document handling features?** KevinSec provides practical web application security testing focused on real-world upload risks, access control failures, and secure remediation.
