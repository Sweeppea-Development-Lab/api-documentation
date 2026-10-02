# Get Drip Campaign

Returns one drip campaign in full: every step with its delay and content, a summary of the audience and the sending settings.

## Endpoint

`POST /messaging/drip`

## Description

Returns the drip identified by `DripToken`, which must belong to your account.

A drip is created, edited, activated and deleted in the Sweeppea app — those actions run the full launch checklist (template, sender, 10DLC, content screening and plan). The API reads drips, pauses and resumes them, and takes a single contact out of a sequence.

Steps are listed in order. `Delay` is the wait after the previous step (after enrolment for the first one).

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. The Drip Marketing module must be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `DripToken` | string | Yes | UUID v4 of the drip. |
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
curl -X POST "https://api-v3.sweeppea.com/messaging/drip" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "DripToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/drip', {
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

url = "https://api-v3.sweeppea.com/messaging/drip"
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
      "Status": "active",
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
      "PausedAt": null,
      "CompletedAt": null,
      "CreationDate": "2026-09-18T10:15:40.000Z",
      "UpdatedAt": "2026-09-20T14:02:11.000Z",
      "Audience": {
        "Sources": [
          "participants"
        ],
        "AcquisitionChannels": [],
        "Groups": 0,
        "Lists": 0,
        "Agents": 0
      },
      "Settings": {
        "Language": "en",
        "SendWindow": null,
        "ExitOnClick": false,
        "ExitOnWin": true,
        "MaxEnrollmentsPerDay": null,
        "HasTemplate": true
      },
      "Steps": [
        {
          "Index": 0,
          "StepToken": "uuid-v4-string",
          "Name": "Welcome",
          "Delay": {
            "Value": 1,
            "Unit": "hours"
          },
          "Content": {
            "Subject": "Welcome, [ FIRST_NAME ]!",
            "Preheader": "Thanks for entering",
            "Blocks": [
              {
                "Type": "text",
                "Text": "Good luck in the drawing!"
              }
            ]
          }
        }
      ]
    }
  },
  "Message": "Drip campaign fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Drip.DripToken` | string | UUID of the drip. |
| `Drip.SweepstakesToken` | string | The sweepstakes the drip belongs to. |
| `Drip.Name / Description` | string | As written in the app. |
| `Drip.Channel` | string | `email` or `sms`. |
| `Drip.Status` | string | `draft`, `active`, `paused`, `completed` or `blocked`. |
| `Drip.BlockedReason` | string|null | Why the platform stopped the drip (plan, 10DLC, a held step…). |
| `Drip.StaffHold` | boolean | `true` when only Sweeppea support can release the drip. |
| `Drip.Trigger` | string | `join` (new contacts), `existing` (everyone already in the audience) or `both`. |
| `Drip.StepsCount` | number | Number of steps (max 10). |
| `Drip.Counters` | object | People: `Enrolled`, `Active`, `Completed`, `Exited` (by reason) and `Deferred`. |
| `Drip.ActivatedAt / PausedAt / CompletedAt` | string|null | ISO 8601 dates of the lifecycle. |
| `Drip.Audience` | object | `Sources` and `AcquisitionChannels`, and how many `Groups`, `Lists` and `Agents` are selected (counts, not tokens). |
| `Drip.Settings` | object | `Language`, `SendWindow`, `ExitOnClick`, `ExitOnWin`, `MaxEnrollmentsPerDay` and `HasTemplate`. |
| `Drip.Steps[]` | array | `Index` (0-based), `StepToken`, `Name`, `Delay` `{ Value, Unit }` and `Content` — `Subject`, `Preheader`, `Blocks` for email; `SmsBody` for SMS. |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: DripToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "DripToken": "string (required) — UUID v4 of the drip",
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

- Read-only.
- Wildcards such as `[ FIRST_NAME ]` are returned as written; they are filled per contact at send time.
