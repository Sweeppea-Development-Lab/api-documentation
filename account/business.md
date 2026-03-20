# Fetch Business Info

Fetch Business Info API endpoint for Sweeppea. This endpoint allows you to fetch business information from the user's profile. Use this to retrieve business-specific data programmatically through the Sweeppea API.

## Endpoint

`POST account/business`

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/account/business" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/account/business', {
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

url = "https://api-v3.sweeppea.com/account/business"
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
  "Data": {
    "BusinessName": "string",
    "BusinessTelephone": "string",
    "BusinessAddress": "string",
    "BusinessCity": "string",
    "BusinessState": "string",
    "BusinessZipCode": "string",
    "BusinessCountry": "string",
    "BusinessWebsite": "string",
    "EIN": "string"
  },
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
  "Message": "User Profile Not Found",
  "Code": 404
}
```
