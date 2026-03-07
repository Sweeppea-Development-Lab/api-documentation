# Fetch Entry Page Fields

Fetch Entry Page Fields API endpoint for Sweeppea. Retrieve complete entry page field configuration using a SweepstakesToken, including form fields, KeyEmail, and KeyPhoneNumber settings.

## Endpoint

`POST /entrypage/fields`

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | Yes | Unique identifier of the sweepstakes (UUID v4) |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/entrypage/fields" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"SweepstakesToken": "uuid-v4-string"}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/entrypage/fields', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    'SweepstakesToken': 'your-sweepstakes-token'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/entrypage/fields"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "SweepstakesToken": "uuid-v4-string"
}

response = requests.post(url, headers=headers, data=json.dumps(data))
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "SweepstakesToken": "uuid-v4-string",
    "EntryPageToken": "uuid-v4-string",
    "FormFields": [
      {
        "FieldID": "email",
        "FieldType": "email",
        "FieldName": "Email Address",
        "FieldPlaceholder": "Enter your email",
        "FieldRequired": true,
        "PrimaryField": true
      },
      {
        "FieldID": "phone",
        "FieldType": "tel",
        "FieldName": "Phone Number",
        "FieldPlaceholder": "Enter your phone",
        "FieldRequired": false,
        "PrimaryField": false
      }
    ],
    "KeyEmail": "email",
    "KeyPhoneNumber": "phone"
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

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing SweepstakesToken In Request Body",
  "Code": 400
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Entry Page Not Found",
  "Code": 404
}
```
