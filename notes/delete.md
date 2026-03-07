# Delete Note

Delete a note owned by the authenticated user.

## Endpoint

`POST /notes/delete`

## Description

This endpoint deletes a note owned by the authenticated user. The note is identified by its NoteToken and can only be deleted by the user who created it.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter   | Type   | Required | Description                                       |
|-------------|--------|----------|---------------------------------------------------|
| `NoteToken` | String | Yes      | Unique identifier of the note to delete (UUID v4) |

## Request Example

```json
{
  "NoteToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/notes/delete" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"NoteToken":"uuid-v4-string"}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/notes/delete', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    NoteToken: 'd1dc1094-918b-4981-923d-c72c51e1344b'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/notes/delete"
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
  "Message": "Note Deleted Successfully",
  "Data": {
    "NoteToken": "uuid-v4-string"
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing Required Field: NoteToken is required",
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
  "Message": "Note Not Found or Access Denied",
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

- **Ownership Verification:** Users can only delete notes they own.
- **Note Identification:** Notes are identified by their unique NoteToken (UUID v4).
- **Permanent Action:** Deletion is permanent and cannot be undone.
- **Not Found:** Returns 404 if note does not exist or belongs to another user.
