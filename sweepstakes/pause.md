# Pause Sweepstakes

Pause a sweepstakes by providing the SweepstakesToken. This endpoint sets the sweepstakes status to inactive without deleting any data.

## Endpoint

`POST sweepstakes/pause`

## Description

This endpoint allows you to pause a sweepstakes. The sweepstakes must belong to the authenticated user.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | Required | The unique identifier of the sweepstakes to pause |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/sweepstakes/pause" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "uuid-v4-string"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/sweepstakes/pause', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: 'uuid-v4-string'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/sweepstakes/pause"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string"
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Message": "Sweepstakes Paused Successfully"
}
```

**401 Unauthorized**
```json
{
  "Response": false,
  "Message": "Missing or invalid Bearer token. Send your API token in the Authorization header as: Authorization: Bearer YOUR_API_TOKEN",
  "Code": 401
}
```

**403 Forbidden**
```json
{
  "Response": false,
  "Message": "Invalid API token. It does not match any account.",
  "Code": 403
}
```

**400 Bad Request**
```json
{
  "Response": false,
  "Message": "Missing Required Parameter: SweepstakesToken",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes"
    }
  },
  "Code": 400
}
```

**404 Not Found**
```json
{
  "Response": false,
  "Message": "Sweepstakes Not Found",
  "Code": 404
}
```
