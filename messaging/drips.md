# List Drip Campaigns

Lists the drip campaigns (automated email or SMS sequences) of your account, with their status, people counters and your plan allowance.

## Endpoint

`POST /messaging/drips`

## Description

Returns one page of drip campaigns, newest first. Filter by sweepstakes, channel, status or name. Archived drips are listed only when `Archived` is `true`.

A drip is created, edited, activated and deleted in the Sweeppea app — those actions run the full launch checklist (template, sender, 10DLC, content screening and plan). The API reads drips, pauses and resumes them, and takes a single contact out of a sequence.

The response also reports your plan allowance (`PlanLimit`) and whether your plan lets drips run (`CanRun`).

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. The Drip Marketing module must be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | string | No | UUID v4. Only drips of this sweepstakes. |
| `Channel` | string | No | `email` or `sms`. |
| `Status` | string | No | `draft`, `active`, `paused`, `completed` or `blocked`. |
| `Search` | string | No | Part of the drip name, max 100 characters. |
| `Archived` | boolean | No | `true` lists archived drips instead. Default `false`. |
| `Page` | number | No | Page number, starting at 1. Default 1. |
| `ItemsPerPage` | number | No | 1 to 100. Default 25. |
| `CalledFromMCP` | boolean | No | Set by the MCP server, ignore it. |
| `CalledFromCLI` | boolean | No | Set by the CLI, ignore it. |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "Status": "active",
  "Page": 1,
  "ItemsPerPage": 25
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/drips" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SweepstakesToken": "uuid-v4-string",
        "Status": "active",
        "Page": 1,
        "ItemsPerPage": 25
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/drips', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "uuid-v4-string",
    Status: "active",
    Page: 1,
    ItemsPerPage: 25
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/messaging/drips"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "Status": "active",
    "Page": 1,
    "ItemsPerPage": 25
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
    "Drips": [
      {
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
      }
    ],
    "TotalResults": 1,
    "Page": 1,
    "ItemsPerPage": 25,
    "TotalPages": 1,
    "PlanLimit": {
      "MaxAllowed": 3,
      "CurrentCount": 2,
      "Unlimited": false,
      "LimitReached": false
    },
    "CanRun": true
  },
  "Message": "Drip campaigns fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Drips` | array | One object per drip — same shape as `Drip` in [Get Drip Campaign](drip.md), without steps, audience or settings. |
| `TotalResults` | number | Drips matching the filters. |
| `Page / ItemsPerPage / TotalPages` | number | Pagination. |
| `PlanLimit.MaxAllowed` | number | Drips your plan allows (drafts and archived count). `0` = unlimited. |
| `PlanLimit.CurrentCount` | number | Drips in your account. |
| `PlanLimit.LimitReached` | boolean | `true` when no more drips can be created. |
| `CanRun` | boolean | Whether your plan lets drips be activated or resumed. |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid Status. Accepted values: draft, active, paused, completed, blocked.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (optional) — UUID v4. Only drips of this sweepstakes (must belong to your account)",
      "Channel": "string (optional) — email | sms",
      "Status": "string (optional) — draft | active | paused | completed | blocked",
      "Search": "string (optional) — Part of the drip name, max 100 characters",
      "Archived": "boolean (optional) — true lists archived drips instead. Defaults to false",
      "Page": "number (optional) — Page number, starting at 1. Defaults to 1",
      "ItemsPerPage": "number (optional) — 1 to 100. Defaults to 25",
      "CalledFromMCP": "boolean (optional) — Set by the MCP server, ignore it",
      "CalledFromCLI": "boolean (optional) — Set by the CLI, ignore it"
    }
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

- Read-only: it never changes a drip.
- Contact addresses never appear in drip responses.
- `Counters` count PEOPLE in the sequence; message statistics are in [Drip Campaign Report](drip-report.md).
