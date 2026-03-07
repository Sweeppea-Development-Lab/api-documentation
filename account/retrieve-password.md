# Retrieve Password

Retrieve Password API endpoint for Sweeppea. This endpoint allows you to retrieve a forgotten password. Use this to manage your account data programmatically through the Sweeppea API.

## Endpoint

`POST /api/v3/account/password/retrieve`

## Request Example

```json
{
  "key": "value",
  "name": "example"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/account/password/retrieve" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "key": "value",
    "name": "example"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/account/password/retrieve', {
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

url = "https://api-v3.sweeppea.com/account/password/retrieve"
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
