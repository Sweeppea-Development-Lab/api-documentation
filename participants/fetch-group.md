# Fetch Groups

Retrieve all groups for a specific sweepstakes. Groups allow you to organize and segment participants within your sweepstakes.

## Endpoint

`POST /groups/fetch`

## Description

This endpoint allows you to fetch all groups for a specific sweepstakes. The groups will be returned in descending order by creation date. Use this to manage your participants & groups data programmatically through the Sweeppea API.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter          | Type             | Required | Description                                             |
|--------------------|------------------|----------|---------------------------------------------------------|
| `sweepstakesToken` | String (UUID v4) | Yes      | The UUID token of the sweepstakes to fetch groups from  |

## Request Example

```json
{
  "sweepstakesToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/groups/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "sweepstakesToken": "uuid-v4-string"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/groups/fetch', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    sweepstakesToken: "uuid-v4-string"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/groups/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "sweepstakesToken": "uuid-v4-string"
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": [
    {
      "GroupToken": "uuid-v4-string",
      "UserToken": "uuid-v4-string",
      "SweepstakesToken": "uuid-v4-string",
      "GroupName": "VIP Members",
      "Primary": false,
      "Locked": false,
      "CreationDate": "2026-01-15T13:16:43.576Z"
    },
    {
      "GroupToken": "uuid-v4-string",
      "UserToken": "uuid-v4-string",
      "SweepstakesToken": "uuid-v4-string",
      "GroupName": "Premium Users",
      "Primary": false,
      "Locked": false,
      "CreationDate": "2026-01-15T13:19:46.133Z"
    }
  ],
  "Count": 2
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: sweepstakesToken"
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
  "Message": "Sweepstakes not found or does not belong to user"
}
```
