# Fetch Files

Fetch all files from the authenticated user's Drive. Returns file metadata, categories, storage usage vs plan quota, and supports pagination.

## Endpoint

`POST /files/fetch`

## Description

This endpoint fetches all files stored in the user's Drive. It returns the file list with full metadata, file categories, current storage usage compared to the plan quota, and supports pagination for large file collections.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `Page` | Number | No | Page number (default: 1) |
| `Limit` | Number | No | Files per page (default: 50, max: 200) |

## Request Example

```json
{
  "Page": 1,
  "Limit": 50
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/files/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Page": 1,
    "Limit": 50
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/files/fetch', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    Page: 1,
    Limit: 50
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/files/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "Page": 1,
    "Limit": 50
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
    "Files": [
      {
        "FileToken": "uuid-v4-string",
        "CategoryToken": "uuid-v4-string",
        "CreationDate": "2024-01-23T21:58:05.468Z",
        "FileInfo": {
          "Filename": "report.pdf",
          "Path": "userToken/documents",
          "Size": 34980,
          "MimeType": "application/pdf",
          "Encoding": "7bit",
          "UploadByAdmin": false,
          "Opened": false,
          "Private": true
        },
        "SharedTo": [],
        "VisibleToAdmins": false
      }
    ],
    "Categories": [
      {
        "CategoryToken": "uuid-v4-string",
        "Category": "General",
        "Color": "#A51400",
        "Primary": true
      }
    ],
    "Storage": {
      "UsedMB": 20.05,
      "MaxMB": 5000,
      "RemainingMB": 4979.95
    },
    "Pagination": {
      "Page": 1,
      "Limit": 50,
      "TotalFiles": 7,
      "TotalPages": 1
    }
  },
  "Message": "(OK) Files fetched successfully."
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

- **Storage Info:** The response includes current storage usage, plan quota, and remaining space in MB.
- **Categories:** File categories are returned alongside the files for easy filtering.
- **Pagination:** Default 50 files per page, maximum 200. Use `Page` and `Limit` parameters to paginate.
- **Sort Order:** Files are sorted by creation date (newest first).
