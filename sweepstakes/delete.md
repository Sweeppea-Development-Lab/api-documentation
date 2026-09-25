# Delete Sweepstakes

Delete a single sweepstakes by providing the SweepstakesToken. This endpoint permanently removes the sweepstakes from the database.

## Endpoint

`POST sweepstakes/delete`

## Description

This endpoint allows you to delete a sweepstakes and all its related data by providing the SweepstakesToken. The sweepstakes must belong to the authenticated user.

**What gets deleted:** Sweepstakes record, participants, entry pages, statistics, groups, rules, calendar events, short links, coupons, automations, surveys (questions, responses, statistics and uploaded media), the AI agents of this sweepstakes (conversations, leads, polls, tasks, knowledge and their files), face validation sessions, and all related data. Email & SMS campaigns of this sweepstakes are also removed; any still sending are stopped first and their unused monthly allowance is returned to the account. Opt-outs (unsubscribes and STOP replies) are kept, because an opt-out applies to the whole account.

> **Note:** This action is permanent and cannot be undone.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | Required | The unique identifier of the sweepstakes to delete |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/sweepstakes/delete" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "uuid-v4-string"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/sweepstakes/delete', {
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

url = "https://api-v3.sweeppea.com/sweepstakes/delete"
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
  "Message": "Sweepstakes Deleted Successfully"
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
