# Delete To-Do

Permanently delete a To-Do item. Admin access required.

## Endpoint

`POST /tickets/deleteTodo`

## Description

This endpoint permanently deletes a To-Do item identified by its `TodoToken`. It is restricted to admin users only. This action cannot be undone — the document is removed from the database immediately upon success.

## Authentication & Authorization

This endpoint is restricted to administrator accounts. Regular users will receive a `403 Forbidden` response.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `TodoToken` | String | Required | UUID v4 of the To-Do to delete |

## Request Example

```json
{
  "TodoToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tickets/deleteTodo" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "TodoToken": "uuid-v4-string"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tickets/deleteTodo', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    TodoToken: 'uuid-v4-string'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/tickets/deleteTodo"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "TodoToken": "uuid-v4-string"
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Message": "To-Do deleted successfully"
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
