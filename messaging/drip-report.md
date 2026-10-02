# Drip Campaign Report

Message statistics of a drip campaign: per step, in total, day by day, and the links people clicked.

## Endpoint

`POST /messaging/drip-report`

## Description

Returns where people are in the sequence and how each step performs: sent, delivered, opened, clicked, bounced, complained, unsubscribed and failed, with rates over sent.

`Days` sets the window of the daily `Timeline` and of `TopLinks` (7, 30, 90 or 180 days). The per-step `Counters` and `Totals` cover the whole life of the drip.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. The Drip Marketing module must be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `DripToken` | string | Yes | UUID v4 of the drip. |
| `Days` | number | No | Activity window: 7, 30, 90 or 180. Default 30. |
| `CalledFromMCP` | boolean | No | Set by the MCP server, ignore it. |
| `CalledFromCLI` | boolean | No | Set by the CLI, ignore it. |

## Request Example

```json
{
  "DripToken": "uuid-v4-string",
  "Days": 30
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/drip-report" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "DripToken": "uuid-v4-string",
        "Days": 30
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/drip-report', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    DripToken: "uuid-v4-string",
    Days: 30
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/messaging/drip-report"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "DripToken": "uuid-v4-string",
    "Days": 30
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
      "UpdatedAt": "2026-09-20T14:02:11.000Z"
    },
    "Days": 30,
    "People": {
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
    "Totals": {
      "Sent": 2310,
      "Delivered": 2281,
      "Opened": 1104,
      "Clicked": 212,
      "Bounced": 21,
      "Complained": 1,
      "Unsubscribed": 38,
      "Failed": 8
    },
    "Rates": {
      "Delivered": 98.74,
      "Opened": 47.79,
      "Clicked": 9.18,
      "Unsubscribed": 1.65,
      "Bounced": 0.91,
      "Complained": 0.04
    },
    "Steps": [
      {
        "Index": 0,
        "Name": "Welcome",
        "Delay": {
          "Value": 1,
          "Unit": "hours"
        },
        "Waiting": 120,
        "Counters": {
          "Sent": 1180,
          "Delivered": 1166,
          "Opened": 640,
          "Clicked": 131,
          "Bounced": 12,
          "Complained": 0,
          "Unsubscribed": 14,
          "Failed": 2
        },
        "Rates": {
          "Delivered": 98.81,
          "Opened": 54.24,
          "Clicked": 11.1,
          "Unsubscribed": 1.19,
          "Bounced": 1.02
        }
      }
    ],
    "Timeline": [
      {
        "Day": "2026-10-01",
        "Sent": 84,
        "Opened": 41,
        "Clicked": 7,
        "Unsubscribed": 1
      }
    ],
    "TopLinks": [
      {
        "Link": "https://example.com/prizes",
        "Step": 1,
        "Clicks": 96
      }
    ]
  },
  "Message": "Drip campaign report fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Drip` | object | The drip summary (see [List Drip Campaigns](drips.md)). |
| `People` | object | Enrolled, active, completed, exited (by reason) and deferred contacts. |
| `Totals` | object | Message counters summed over every step. |
| `Rates` | object | Percentages over `Sent`, two decimals. |
| `Steps[]` | array | Per step: `Index`, `Name`, `Delay`, `Waiting` (people waiting for it), `Counters` and `Rates`. |
| `Timeline[]` | array | One entry per UTC day of the window: `Day`, `Sent`, `Opened`, `Clicked`, `Unsubscribed`. |
| `TopLinks` | array|null | Up to 10 most-clicked links in the window with their 1-based `Step`. The unsubscribe link is excluded. `null` if the links could not be read. |

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
      "Days": "number (optional) — The activity window: 7, 30, 90, 180. Defaults to 30",
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

- Test sends are excluded.
- Daily rows are UTC days.
- Message rows are kept 180 days, which is why the window stops there.
