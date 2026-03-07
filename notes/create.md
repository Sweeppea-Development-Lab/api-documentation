# Create Note

Create a new encrypted note for the authenticated user. Note content is automatically encrypted before storage.

## Endpoint

`POST /notes/create`

## Description

This endpoint creates a new note for the authenticated user. The note content is automatically encrypted using AES-256-CBC encryption before being stored in the database. Each note is assigned a unique NoteToken (UUID v4) for identification.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type    | Required | Description                                              |
|-----------|---------|----------|----------------------------------------------------------|
| `Title`   | String  | Yes      | Note title (max 100 characters)                          |
| `Note`    | String  | Yes      | Note content (max 100,000 characters, will be encrypted) |
| `Pinned`  | Boolean | No       | Pin note to top (default: false)                         |

## Request Example

```json
{
  "Title": "My Important Note",
  "Note": "This is the content of my note that will be encrypted",
  "Pinned": false
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/notes/create" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Title": "My Important Note",
    "Note": "This is the content of my note that will be encrypted",
    "Pinned": false
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/notes/create', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    Title: 'My Important Note',
    Note: 'This is the content of my note that will be encrypted',
    Pinned: false
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/notes/create"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "Title": "My Important Note",
    "Note": "This is the content of my note that will be encrypted",
    "Pinned": False
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**201 Created**

```json
{
  "Response": true,
  "Message": "Note Created Successfully",
  "Data": {
    "NoteToken": "uuid-v4-string",
    "Title": "My Important Note",
    "CreationDate": "2026-01-16T11:49:43.140Z",
    "Pinned": false
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing Required Fields: Title and Note are required",
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

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal Server Error",
  "Code": 500
}
```

## Notes

- **Encryption:** Note content is automatically encrypted using AES-256-CBC encryption before storage.
- **Unique Identifier:** Each note receives a unique NoteToken (UUID v4) for identification.
- **Title Limit:** Maximum length of 100 characters.
- **Content Limit:** Maximum length of 100,000 characters.
- **Unique Titles:** Note titles must be unique per user — duplicate titles are not allowed.
- **User Association:** Notes are automatically associated with the authenticated user's UserToken.
- **Pinned Notes:** Pinned notes will appear at the top of the notes list.
