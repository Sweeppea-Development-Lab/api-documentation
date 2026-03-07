# Create To-Do

Create a new To-Do item. Admin access required.

## Endpoint

`POST /tickets/createTodo`

## Description

This endpoint creates a new To-Do item in the system. It is restricted to admin users only. On success, it returns the full created To-Do document including the generated `TodoToken` and `CreationDate`.

## Authentication & Authorization

This endpoint is restricted to administrator accounts. Regular users will receive a `403 Forbidden` response.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `Title` | String | Required | Title of the To-Do (max 200 characters) |
| `Priority` | Number | Required | `1` = Low, `2` = Medium, `3` = High |
| `Description` | String | Optional | Detailed description of the To-Do (max 20,000 characters) |
| `ResourceAffected` | String | Optional | Resource this To-Do relates to (e.g. `"renaissance"`, `"api"`, `"aws"`, `"general"`). Defaults to `"general"` |
| `Pin` | Boolean | Optional | Pin this To-Do to the top. Defaults to `false` |

## Request Example

```json
{
  "Title": "Deploy new API endpoint",
  "Priority": 3,
  "Description": "Deploy the createTodo lambda to production",
  "ResourceAffected": "api",
  "Pin": false,
  "Deadline": "2025-12-31"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tickets/createTodo" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Title": "Deploy new API endpoint",
    "Priority": 3,
    "Description": "Deploy the createTodo lambda to production",
    "ResourceAffected": "api",
    "Pin": false,
    "Deadline": "2025-12-31"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tickets/createTodo', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    Title: 'Deploy new API endpoint',
    Priority: 3,
    Description: 'Deploy the createTodo lambda to production',
    ResourceAffected: 'api',
    Pin: false,
    Deadline: '2025-12-31'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/tickets/createTodo"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "Title": "Deploy new API endpoint",
    "Priority": 3,
    "Description": "Deploy the createTodo lambda to production",
    "ResourceAffected": "api",
    "Pin": False,
    "Deadline": "2025-12-31"
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
    "Deadline": "2025-12-31T00:00:00.000Z",
    "Title": "Deploy new API endpoint",
    "Description": "Deploy the createTodo lambda to production",
    "Priority": 3,
    "ResourceAffected": "api",
    "Pin": false,
    "Status": false,
    "Views": 0,
    "Completion": 0
  }
}
```

**400 Bad Request**
```json
{
  "Response": false,
  "Message": "Missing Required Field: Title",
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

**500 Internal Server Error**
```json
{
  "Response": false,
  "Message": "Internal Server Error",
  "Code": 500
}
```
