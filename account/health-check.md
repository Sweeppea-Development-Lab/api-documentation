# Health Check

Health Check API endpoint for Sweeppea. This endpoint allows you to verify that the API is healthy and your API token is valid. Use this for monitoring purposes or to check the connection status before making other API calls. This is a lightweight endpoint that requires only authentication and returns a simple success message.

## Endpoint

`POST account/health-check`

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/account/health-check" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/account/health-check', {
  method: 'POST',
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

url = "https://api-v3.sweeppea.com/account/health-check"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

response = requests.post(url, headers=headers)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "API is healthy",
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 142,
    "MaxAPICalls": 500000
  }
}
```


**401 Unauthorized**

```json
{
  "Response": false,
  "Message": "Invalid or Missing Bearer Token"
}
```

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal Server Error"
}
```
