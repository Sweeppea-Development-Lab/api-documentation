# Delete File

Delete a file from the authenticated user's Drive. Permanently removes the file from S3 storage and the database.

## Endpoint

`POST /files/delete`

## Description

This endpoint permanently deletes a file from the user's Drive. It validates ownership of the file, removes the file object from S3 storage, and deletes the corresponding document from the database. This action cannot be undone.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `FileToken` | String | Yes | UUID v4 of the file to delete |

## Request Example

```json
{
  "FileToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/files/delete" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "FileToken": "uuid-v4-string"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/files/delete', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    FileToken: 'uuid-v4-string'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/files/delete"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "FileToken": "uuid-v4-string"
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
    "DataConsumed": 0,
    "APICalls": 42,
    "MaxAPICalls": 1500000
  },
  "Data": {
    "FileToken": "uuid-v4-string",
    "Filename": "report.pdf",
    "SizeMB": 0.03
  },
  "Message": "(OK) File deleted successfully."
}
```

**400 Bad Request** — Missing or invalid FileToken

```json
{
  "Response": false,
  "Message": "Invalid or Missing FileToken.",
  "Help": {
    "ExpectedBody": {
      "FileToken": "string (required) — UUID v4 of the file to delete"
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

- **Permanent Deletion:** This action permanently removes the file from both S3 storage and the database. It cannot be undone.
- **Ownership Validation:** Only the file owner can delete it. The endpoint verifies that the `FileToken` belongs to the authenticated user.
- **Storage Freed:** The file size is freed from the user's storage quota immediately after deletion.
