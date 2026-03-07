# Delete Rules

Delete official rules for a specific sweepstakes. The rule must belong to the authenticated user and the specified sweepstakes.

## Endpoint

`POST /rules/delete`

## Description

This endpoint deletes a specific official rule document. The rule must exist for the authenticated user and the specified sweepstakes token. All three parameters (UserToken via API token, SweepstakesToken, and RulesToken) must match for the deletion to succeed.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String (UUID v4) | Yes | The unique identifier of the sweepstakes |
| `RulesToken` | String (UUID v4) | Yes | The unique identifier of the rule to delete |

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/rules/delete" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "YOUR_SWEEPSTAKES_TOKEN",
    "RulesToken": "YOUR_RULES_TOKEN"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/rules/delete', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: 'YOUR_SWEEPSTAKES_TOKEN',
    RulesToken: 'YOUR_RULES_TOKEN'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/rules/delete"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "YOUR_SWEEPSTAKES_TOKEN",
    "RulesToken": "YOUR_RULES_TOKEN"
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "Rule Deleted Successfully"
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "SweepstakesToken is Required",
  "Code": 400
}
```

```json
{
  "Response": false,
  "Message": "RulesToken is Required",
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
  "Message": "Invalid API Token",
  "Code": 403
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Rule Not Found",
  "Code": 404
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
