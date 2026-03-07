# Update Note

Update an existing note owned by the authenticated user. Supports partial updates.

## Endpoint

`POST /notes/update`

## Description

This endpoint updates an existing note for the authenticated user. You can update the title, content, or pinned status. The note content is automatically re-encrypted if updated. All fields except NoteToken are optional, allowing partial updates.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter   | Type    | Required | Description                                                  |
|-------------|---------|----------|--------------------------------------------------------------|
| `NoteToken` | String  | Yes      | Unique identifier of the note to update (UUID v4)            |
| `Title`     | String  | No       | New note title (max 100 characters)                          |
| `Note`      | String  | No       | New note content (max 100,000 characters, will be encrypted) |
| `Pinned`    | Boolean | No       | Pin/unpin note                                               |

## Request Example

```json
{
  "NoteToken": "uuid-v4-string",
  "Title": "Updated Title",
  "Note": "Updated content",
  "Pinned": true
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/notes/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"NoteToken":"uuid-v4-string","Title":"Updated Title","Note":"Updated content","Pinned":true}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/notes/update', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    NoteToken: 'uuid-v4-string',
    Title: 'Updated Title',
    Note: 'Updated content',
    Pinned: true
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/notes/update"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "NoteToken": "uuid-v4-string",
    "Title": "Updated Title",
    "Note": "Updated content",
    "Pinned": True
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "Note Updated Successfully",
  "Data": {
    "NoteToken": "uuid-v4-string",
    "Title": "Updated Title",
    "Pinned": true,
    "CreationDate": "2026-01-16T11:49:43.140Z"
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

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "No Fields to Update",
  "Code": 400
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Title Exceeds Maximum Length of 100 Characters",
  "Code": 400
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Note Exceeds Maximum Length of 100000 Characters",
  "Code": 400
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Note Title Already Exists",
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

- **Encryption:** Note content is automatically re-encrypted using AES-256-CBC if updated.
- **Ownership Verification:** Users can only update notes they own.
- **Partial Updates:** You can update only the fields you want to change (Title, Note, or Pinned).
- **Note Identification:** Notes are identified by their unique NoteToken (UUID v4).
- **Title Limit:** Maximum length of 100 characters.
- **Content Limit:** Maximum length of 100,000 characters.
- **Unique Titles:** Note titles must be unique per user — duplicate titles are not allowed.
- **Not Found:** Returns 404 if note does not exist or belongs to another user.
