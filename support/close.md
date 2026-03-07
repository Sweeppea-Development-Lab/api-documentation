# Close Support Ticket

Close a support ticket via the Sweeppea API.

## Endpoint

`POST /support/tickets/{id}/close`

## Description

This endpoint allows you to close a support ticket. Use this to manage your help and support data programmatically through the Sweeppea API.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `key` | String | No | Key field for the close operation |
| `name` | String | No | Name field for the close operation |

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/support/tickets/{id}/close" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "key": "value",
    "name": "example"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/support/tickets/{id}/close', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    key: "value",
    name: "example"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/support/tickets/{id}/close"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "key": "value",
    "name": "example"
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

## Response

**200 OK**

```json
{
  "success": true,
  "data": {},
  "message": "Operation completed successfully"
}
```
