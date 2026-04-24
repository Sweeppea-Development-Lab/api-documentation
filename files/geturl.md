# Get File URL

Generate a short-lived presigned S3 URL for a file in the user's Drive, enabling direct download or inline preview without exposing the underlying private bucket.

## Endpoint

`POST /files/getFileUrl`

## Description

Files uploaded to Sweeppea Drive are stored in a private S3 bucket with server-side encryption and strict ACLs — they are not accessible via public URLs. This endpoint produces a temporary, signed URL that grants read-only access to one specific file for a limited time window. Use it to render images, embed PDFs, stream media previews, or trigger browser downloads, without ever proxying the bytes through the API. The signed URL embeds the correct `Content-Type` and `Content-Disposition` so browsers handle the response consistently. Ownership is enforced: the file's `UserToken` must match the authenticated user.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `FileToken` | String | Yes | UUID v4 of the file to generate a URL for |
| `Mode` | String | No | `"preview"` (inline, default) or `"download"` (forces the browser to save as attachment) |
| `ExpiresIn` | Number | No | URL lifetime in seconds. Range: 60–3600. Default: 900 (15 minutes) |

## Behavior

- `Mode: "preview"` — the URL renders inline in the browser (useful for `<img>`, `<iframe>` PDF/video preview).
- `Mode: "download"` — the URL forces the browser to download the file with its original filename.
- The presigned URL embeds the response `Content-Type` and `Content-Disposition` so the browser behaves correctly regardless of how the S3 object was stored.

## Request Example

```json
{
  "FileToken": "uuid-v4-string",
  "Mode": "download",
  "ExpiresIn": 600
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/files/getFileUrl" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "FileToken": "uuid-v4-string",
    "Mode": "download",
    "ExpiresIn": 600
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/files/getFileUrl', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    FileToken: 'uuid-v4-string',
    Mode: 'preview',
    ExpiresIn: 900
  })
});

const { Data } = await response.json();

// Open in a new tab or redirect the browser
window.open(Data.Url, '_blank');

// Or use as an image / iframe source
// document.getElementById('preview').src = Data.Url;
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/files/getFileUrl"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "FileToken": "uuid-v4-string",
    "Mode": "download",
    "ExpiresIn": 600
}

response = requests.post(url, headers=headers, json=payload)
signed_url = response.json()["Data"]["Url"]

# Download the file directly from S3 using the signed URL
file_response = requests.get(signed_url)
with open("downloaded-file", "wb") as f:
    f.write(file_response.content)
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 42,
    "MaxAPICalls": 1500000
  },
  "Data": {
    "FileToken": "uuid-v4-string",
    "Filename": "report.pdf",
    "MimeType": "application/pdf",
    "Size": 1048576,
    "SizeMB": 1.0,
    "Mode": "preview",
    "Url": "https://sweeppea-4.0-production.s3.us-east-1.amazonaws.com/...&X-Amz-Signature=...",
    "ExpiresIn": 900,
    "ExpiresAt": "2026-04-24T15:40:00.000Z"
  },
  "Message": "(OK) File URL generated successfully."
}
```

**400 Bad Request** — Missing or invalid fields

```json
{
  "Response": false,
  "Message": "Invalid or Missing FileToken.",
  "Help": {
    "ExpectedBody": {
      "FileToken": "string (required) — UUID v4 of the file to generate URL for",
      "Mode": "string (optional) — \"download\" (forces attachment) or \"preview\" (inline). Default: \"preview\"",
      "ExpiresIn": "number (optional) — URL lifetime in seconds. Range: 60–3600. Default: 900 (15 minutes)"
    }
  }
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

**404 Not Found** — File not found or access denied

```json
{
  "Response": false,
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 42,
    "MaxAPICalls": 1500000
  },
  "Message": "File not found or access denied.",
  "Code": 404
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

- **Private by Design:** The S3 bucket is private with server-side encryption. Only presigned URLs can read a file, and only for the requested duration.
- **Short-Lived:** URLs expire quickly (default 15 minutes). Regenerate on demand when users interact with a file rather than pre-fetching URLs for long lists.
- **Preview vs Download:** Use `Mode: "preview"` for `<img>`, `<iframe>` PDF viewers, or HTML5 video. Use `Mode: "download"` to force the browser's Save dialog with the original filename.
- **Zero Egress Overhead:** The file flows directly from S3 to the client. This endpoint never proxies bytes, so there is no Lambda timeout or payload size limit.
- **Ownership Validation:** Only the file owner can obtain a URL. The endpoint verifies that the `FileToken` belongs to the authenticated user.
- **Does Not Count as Data Transfer:** This endpoint reports `DataConsumed: 0`; the actual transfer happens directly between the client and S3.
