# Fetch Rules

Retrieve official rules for a specific sweepstakes. Returns primary and secondary rules associated with the sweepstakes.

## Endpoint

`POST /rules/fetch`

## Description

This endpoint retrieves all official rules associated with a specific sweepstakes for the authenticated user. Rules are returned with a primary rule (if exists) and an array of secondary rules. Rules are sorted by primary status and creation date.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String (UUID v4) | Yes | The unique identifier of the sweepstakes |

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/rules/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"SweepstakesToken":"YOUR_SWEEPSTAKES_TOKEN"}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/rules/fetch', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: 'YOUR_SWEEPSTAKES_TOKEN'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/rules/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "YOUR_SWEEPSTAKES_TOKEN"
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "TotalRules": 3,
    "PrimaryRule": {
      "RulesToken": "uuid-v4-string",
      "SweepstakesToken": "uuid-v4-string",
      "Title": "Official Rules - Main",
      "DocumentContent": "Full HTML content of official rules...",
      "AbbrebiatedRulesForShopify": "Abbreviated version...",
      "AbbreviatedRulesForShopify": "Abbreviated version...",
      "Metadata": {},
      "EntryPeriods": [],
      "Views": [],
      "Status": true,
      "Primary": true,
      "CreationDate": "2025-10-02T23:16:57.491Z"
    },
    "SecondaryRules": [
      {
        "RulesToken": "uuid-v4-string-2",
        "SweepstakesToken": "uuid-v4-string",
        "Title": "Official Rules - Alternative",
        "DocumentContent": "Alternative rules content...",
        "Status": true,
        "Primary": false,
        "CreationDate": "2025-10-03T10:20:30.123Z"
      }
    ],
    "SweepstakesToken": "uuid-v4-string"
  }
}
```

**Note:** `AbbreviatedRulesForShopify` (preferred) and `AbbrebiatedRulesForShopify` (legacy field name with a historical typo, kept for backward compatibility) always contain the same value.

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "SweepstakesToken is Required",
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

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal Server Error",
  "Code": 500
}
```
