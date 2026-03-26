# Update Bonus Entries

Overwrite the bonus entries value for a specific participant in a sweepstakes.

## Endpoint

`POST /participants/updateBonusEntries`

## Description

This endpoint overwrites the `BonusEntries` field for a participant identified by `ParticipantToken` within a sweepstakes identified by `SweepstakesToken`. The sweepstakes must belong to the authenticated user. The new value must be an integer between **0** and **1,000,000**.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter          | Type             | Required | Description                                                   |
|--------------------|------------------|----------|---------------------------------------------------------------|
| `SweepstakesToken` | String (UUID v4) | Yes      | Unique identifier for the sweepstakes                         |
| `ParticipantToken` | String (UUID v4) | Yes      | Unique identifier for the participant                         |
| `BonusEntries`     | Number (integer) | Yes      | New bonus entries value — integer between 0 and 1,000,000    |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "ParticipantToken": "uuid-v4-string",
  "BonusEntries": 50
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/participants/updateBonusEntries" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "uuid-v4-string",
    "ParticipantToken": "uuid-v4-string",
    "BonusEntries": 50
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/participants/updateBonusEntries', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: 'uuid-v4-string',
    ParticipantToken: 'uuid-v4-string',
    BonusEntries: 50
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/participants/updateBonusEntries"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "ParticipantToken": "uuid-v4-string",
    "BonusEntries": 50
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 42,
    "MaxAPICalls": 10000
  },
  "Data": {
    "ParticipantToken": "uuid-v4-string",
    "SweepstakesToken": "uuid-v4-string",
    "BonusEntries": 50
  },
  "Message": "(OK) Bonus entries updated successfully."
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid BonusEntries value. Must be an integer between 0 and 1,000,000.",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) — UUID v4 of the sweepstakes",
      "ParticipantToken": "string (required) — UUID v4 of the participant",
      "BonusEntries": "number (required) — New bonus entries value, between 0 and 1,000,000"
    }
  }
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Participant Not Found in This Sweepstakes",
  "Code": 404
}
```
