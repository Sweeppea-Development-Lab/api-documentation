# Fetch Profile Info

Fetch Profile Info API endpoint for Sweeppea. This endpoint allows you to fetch user profile information including personal and business data. Use this to retrieve complete user account information programmatically through the Sweeppea API.

## Endpoint

`POST account/profile`

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/account/profile" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/account/profile', {
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

url = "https://api-v3.sweeppea.com/account/profile"
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
    "UserToken": "uuid-v4-string",
    "Nickname": "string",
    "Email": "string",
    "UserType": 1,
    "Status": true,
    "CreationDate": "2024-01-01T00:00:00.000Z",
    "LastLogin": "2024-01-01T00:00:00.000Z",
    "IsShopifyAccount": false,
    "ProfileData": {
      "FullName": "string",
      "MobilePhone": "string",
      "Address": "string",
      "City": "string",
      "State": "string",
      "ZipCode": "string",
      "Country": "string",
      "Avatar": "string",
      "BirthDate": "2024-01-01T00:00:00.000Z"
    }
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
