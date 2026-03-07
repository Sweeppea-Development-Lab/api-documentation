# Fetch Data Transfer

Retrieve data transfer information for the authenticated user's billing account.

## Endpoint

`GET /billing/transfer`

## Description

This endpoint allows you to fetch data transfer records. Use this to manage your billing data programmatically through the Sweeppea API.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Code Examples

### cURL

```bash
curl -X GET "https://api-v3.sweeppea.com/billing/transfer" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/billing/transfer', {
  method: 'GET',
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

url = "https://api-v3.sweeppea.com/billing/transfer"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

response = requests.get(url, headers=headers)
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
