# Upload File

Upload a file to the authenticated user's Drive. Validates file type against a whitelist, enforces a 10 MB size limit, checks storage quota, and sanitizes the filename before storing.

## Endpoint

`POST /files/upload`

## Description

This endpoint uploads a file to the user's Drive. The file content must be sent as a base64-encoded string in the request body. The endpoint validates the MIME type and extension against a whitelist of allowed types, checks that the file does not exceed 10 MB, verifies the user has sufficient storage quota, and normalizes the filename with the platform-wide standard (unsafe characters become underscores, accents are transliterated, casing is preserved, and no timestamp is appended). The filename must be unused in the account: uploading a name that already exists returns a `409` instead of overwriting the stored file. If no file category exists, a default "General" category is automatically created. Files are uploaded as private by default.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `Filename` | String | Yes | Original filename with extension (e.g. `"report.pdf"`) |
| `MimeType` | String | Yes | MIME type of the file (e.g. `"application/pdf"`) |
| `FileData` | String | Yes | Base64-encoded file content |
| `Private` | Boolean | No | Whether the file is private (default: `true`) |

## Request Example

```json
{
  "Filename": "report.pdf",
  "MimeType": "application/pdf",
  "FileData": "JVBERi0xLjQKJeLjz9MK...",
  "Private": true
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/files/upload" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Filename": "report.pdf",
    "MimeType": "application/pdf",
    "FileData": "JVBERi0xLjQKJeLjz9MK...",
    "Private": true
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/files/upload', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    Filename: 'report.pdf',
    MimeType: 'application/pdf',
    FileData: 'JVBERi0xLjQKJeLjz9MK...',
    Private: true
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import base64

# Read and encode the file
with open("report.pdf", "rb") as f:
    file_data = base64.b64encode(f.read()).decode("utf-8")

url = "https://api-v3.sweeppea.com/files/upload"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "Filename": "report.pdf",
    "MimeType": "application/pdf",
    "FileData": file_data,
    "Private": True
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Telemetry": {
    "DataConsumed": 0.000033,
    "APICalls": 42,
    "MaxAPICalls": 1500000
  },
  "Data": {
    "FileToken": "uuid-v4-string",
    "Filename": "report.pdf",
    "MimeType": "application/pdf",
    "Size": 34980,
    "SizeMB": 0.03,
    "Path": "userToken/documents/report.pdf",
    "CategoryToken": "uuid-v4-string",
    "Category": "General",
    "Private": true,
    "CreationDate": "2024-01-23T21:58:05.468Z"
  },
  "Message": "(OK) File uploaded successfully."
}
```

**400 Bad Request** — Missing or invalid fields

```json
{
  "Response": false,
  "Message": "Missing or invalid Filename.",
  "Help": {
    "ExpectedBody": {
      "Filename": "string (required) — Original filename with extension (e.g. \"report.pdf\")",
      "MimeType": "string (required) — MIME type of the file (e.g. \"application/pdf\")",
      "FileData": "string (required) — Base64-encoded file content",
      "Private": "boolean (optional) — Whether the file is private (default: true)"
    }
  }
}
```

**400 Bad Request** — File type not allowed

```json
{
  "Response": false,
  "Message": "File type \"application/zip\" is not allowed. Allowed types: PDF, DOC, DOCX, XLS, XLSX, PPT, PPTX, TXT, CSV, JPG, JPEG, PNG, GIF, WEBP, SVG, BMP."
}
```

**400 Bad Request** — File too large

```json
{
  "Response": false,
  "Message": "File size (12.50 MB) exceeds the maximum allowed size of 10 MB."
}
```

**400 Bad Request** — Storage quota exceeded

```json
{
  "Response": false,
  "Message": "Storage quota exceeded. Current usage: 4990.00 MB, file size: 15.00 MB, quota: 5000 MB, remaining: 10.00 MB."
}
```

**409 Conflict** — Filename already in use

```json
{
  "Response": false,
  "Message": "A file named \"report.pdf\" already exists in this account's Drive. Rename the file or delete the existing one and try again.",
  "ExistingFileToken": "uuid-v4-string"
}
```

**401 Unauthorized**

```json
{
  "Response": false,
  "Message": "Invalid or Missing Bearer Token",
  "Code": 401
}
```

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "Invalid API Token",
  "Code": 403
}
```

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal Server Error",
  "Code": 500
}
```

## Notes

- **Max File Size:** 10 MB per file. Files exceeding this limit are rejected.
- **Allowed Types:** PDF, DOC, DOCX, XLS, XLSX, PPT, PPTX, TXT, CSV, JPG, JPEG, PNG, GIF, WEBP, SVG, BMP.
- **Privacy:** Files are uploaded as private by default. Set `Private: false` to make a file accessible.
- **Filename Sanitization:** Only the basename is kept (path traversal sequences are stripped), accents are transliterated (`Ñandú.pdf` becomes `Nandu.pdf`), anything outside `A-Za-z0-9._-` is replaced with an underscore, and the original casing is preserved. No timestamp and no random suffix are appended — the name you send is the name stored.
- **Unique Filenames:** A filename can only exist once per account. The Drive folder in S3 is flat (`{UserToken}/documents/`) and categories are labels on the record rather than folders, so the same name cannot be reused in a different category. Different accounts are unaffected.
- **Auto-Category:** If the user has no file categories, a default "General" category is automatically created and assigned.
- **Storage Quota:** The upload is rejected if it would exceed the user's plan storage quota.
