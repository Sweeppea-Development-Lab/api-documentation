# Update To-Do

Update an existing To-Do item. Admin access required.

## Endpoint

`POST /tickets/updateTodo`

## Description

This endpoint updates an existing To-Do item identified by its `TodoToken`. It is restricted to admin users only. All fields except `TodoToken` are optional — only the fields you provide will be updated (partial update). On success, it returns the full updated To-Do document.

## Authentication & Authorization

This endpoint is restricted to administrator accounts. Regular users will receive a `403 Forbidden` response.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `TodoToken` | String | Required | UUID v4 of the To-Do to update |
| `Title` | String | Optional | New title (max 200 characters) |
| `Description` | String | Optional | New description (max 20,000 characters) |
| `Priority` | Number | Optional | `1` = Low, `2` = Medium, `3` = High |
| `ResourceAffected` | String | Optional | Resource this To-Do relates to (e.g. `"renaissance"`, `"api"`, `"aws"`, `"general"`) |
| `Pin` | Boolean | Optional | Pin this To-Do to the top |
| `Deadline` | String | Optional | ISO date string, or `null` to clear the deadline |
| `Status` | Boolean | Optional | `false` = Pending, `true` = Completed |
| `Completion` | Number | Optional | Completion percentage 0–100 |

## Request Example

```json
{
  "TodoToken": "uuid-v4-string",
  "Title": "Updated title",
  "Priority": 2,
  "Status": true,
  "Completion": 100
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tickets/updateTodo" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "TodoToken": "uuid-v4-string",
    "Title": "Updated title",
    "Priority": 2,
    "Status": true,
    "Completion": 100
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tickets/updateTodo', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    TodoToken: 'uuid-v4-string',
    Title: 'Updated title',
    Priority: 2,
    Status: true,
    Completion: 100
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/tickets/updateTodo"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "TodoToken": "uuid-v4-string",
    "Title": "Updated title",
    "Priority": 2,
    "Status": True,
    "Completion": 100
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Data": {
    "TodoToken": "uuid-v4-string",
    "CreationDate": "2025-06-01T10:00:00.000Z",
    "Deadline": null,
    "Title": "Updated title",
    "Description": "...",
    "Priority": 2,
    "ResourceAffected": "api",
    "Pin": true,
    "Status": true,
    "Views": 3,
    "Completion": 100
  }
}
```

**400 Bad Request**
```json
{
  "Response": false,
  "Message": "Missing or Invalid TodoToken",
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
  "Message": "Admin Access Required",
  "Code": 403
}
```

**404 Not Found**
```json
{
  "Response": false,
  "Message": "To-Do Not Found",
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
