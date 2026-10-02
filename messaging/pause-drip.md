# Pause Drip Campaign

Pauses an active drip campaign: no new contact is enrolled and no step is sent until it is resumed.

## Endpoint

`POST /messaging/pause-drip`

## Description

Only an `active` drip can be paused. Pausing is immediate and atomic: steps already handed to delivery finish, nothing new is queued, and the allowance reserved for queued messages is returned to your monthly balance (`ReleasedUnits`).

Contacts keep their place in the sequence. Resume with [Resume Drip Campaign](resume-drip.md), or in the app.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Send Message module and the Drip Marketing module must be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `DripToken` | string | Yes | UUID v4 of a drip in status `active`. |
| `CalledFromMCP` | boolean | No | Set by the MCP server, ignore it. |
| `CalledFromCLI` | boolean | No | Set by the CLI, ignore it. |

## Request Example

```json
{
  "DripToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/pause-drip" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "DripToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/pause-drip', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    DripToken: "uuid-v4-string"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/messaging/pause-drip"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "DripToken": "uuid-v4-string"
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 312,
    "MaxAPICalls": 1500000
  },
  "Data": {
    "Drip": {
      "DripToken": "uuid-v4-string",
      "SweepstakesToken": "uuid-v4-string",
      "Name": "Welcome Series",
      "Description": "Three emails for new entrants",
      "Channel": "email",
      "Status": "paused",
      "BlockedReason": null,
      "StaffHold": false,
      "Archived": false,
      "Trigger": "join",
      "StepsCount": 3,
      "Counters": {
        "Enrolled": 1240,
        "Active": 812,
        "Completed": 356,
        "Exited": {
          "Unsubscribed": 38,
          "Suppressed": 9,
          "Won": 4,
          "Removed": 2
        },
        "Deferred": {}
      },
      "Origin": "user",
      "ActivatedAt": "2026-09-20T14:02:11.000Z",
      "PausedAt": "2026-10-02T17:20:00.000Z",
      "CompletedAt": null,
      "CreationDate": "2026-09-18T10:15:40.000Z",
      "UpdatedAt": "2026-09-20T14:02:11.000Z"
    },
    "ReleasedUnits": 42
  },
  "Message": "Drip campaign paused successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Drip` | object | The drip after pausing (see [List Drip Campaigns](drips.md)). |
| `ReleasedUnits` | number | Messages of allowance returned to your monthly balance. |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: DripToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "DripToken": "string (required) — UUID v4 of a drip in status active",
      "CalledFromMCP": "boolean (optional) — Set by the MCP server, ignore it",
      "CalledFromCLI": "boolean (optional) — Set by the CLI, ignore it"
    }
  }
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Drip not found. The DripToken must exist and belong to your account.",
  "Code": 404
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This drip cannot be paused: its status is \"draft\". Only an active drip can be paused.",
  "Code": 409,
  "Data": {
    "Status": "draft"
  }
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

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "The Send Message module is not enabled for your account. Contact support to enable it.",
  "Code": 403
}
```

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "The Drip Marketing module is not enabled for your account. Contact support to enable it.",
  "Code": 403
}
```

**429 Too Many Requests**

```json
{
  "Response": false,
  "Message": "Rate limit exceeded. Your plan allows 60 API calls per minute. Wait a few seconds and try again.",
  "Code": 429
}
```

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal server error. Please try again; if it persists, contact support with the time of this request.",
  "Code": 500
}
```

Every `400` carries a `Help.ExpectedBody` block listing every accepted parameter. A disabled or hibernating account and an exhausted monthly API quota answer `403`; the per-minute limit answers `429` — see [Concurrency & Rate Limiting](../concurrency.md).

## Notes

- Recorded in your account log.
- Calling it twice answers `409` the second time; the drip stays paused.
