# Fetch Single Note

Retrieve a single note by its NoteToken. The note content is automatically decrypted before being returned.

## Endpoint

`POST /notes/single`

## Description

This endpoint retrieves a single note owned by the authenticated user. The note is identified by its NoteToken and can only be fetched by the user who created it. The `Note` field is automatically decrypted before being returned.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter   | Type   | Required | Description                                             |
|-------------|--------|----------|---------------------------------------------------------|
| `NoteToken` | String | Yes      | Unique identifier of the note to retrieve (UUID v4)     |

## Request Example

```json
{
  "NoteToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/notes/single" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"NoteToken":"uuid-v4-string"}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/notes/single', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    NoteToken: 'uuid-v4-string'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/notes/single"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "NoteToken": "uuid-v4-string"
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
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
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "NoteToken is Required",
  "Code": 400
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

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Note Not Found",
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

- **Ownership Verification:** Users can only fetch notes they own.
- **Auto Decryption:** The `Note` field is stored encrypted and automatically decrypted in the response.
- **Note Identification:** Notes are identified by their unique NoteToken (UUID v4).
- **Not Found:** Returns 404 if the note does not exist or belongs to another user.
