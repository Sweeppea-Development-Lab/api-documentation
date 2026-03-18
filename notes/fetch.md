# Fetch Notes

Retrieve all notes for the authenticated user. Returns decrypted notes without password protection.

## Endpoint

`POST /notes/fetch`

## Description

This endpoint retrieves all notes associated with the authenticated user. Notes are automatically decrypted and returned in descending order by creation date. Password-protected notes are excluded from the results.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/notes/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/notes/fetch', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/notes/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

response = requests.post(url, headers=headers)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "TotalNotes": 8,
    "Notes": [
      {
        "_id": "68df07e9896a685bf51d58cf",
        "NoteToken": "uuid-v4-string",
        "Title": "Example Note",
        "Note": "Decrypted note content",
        "Views": 0,
        "Pinned": false,
        "Status": false,
        "CreatedByAdmin": false,
        "CreationDate": "2025-10-02T23:16:57.491Z"
      }
    ]
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

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal Server Error",
  "Code": 500
}
```
