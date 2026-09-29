# Fetch Messaging Usage

Everything an integration needs to know before it asks the Messaging Engine for anything: module access, plan channels, the monthly allowance, SMS sender readiness and any active pause.

## Endpoint

`POST /messaging/usage`

## Description

This endpoint answers in one call the questions that would otherwise surface as a failed send: is the Send Message module enabled, which channels does the plan include, how much of this month's email and SMS allowance is left, can SMS go out (a validated 10DLC number), and has Sweeppea paused sending on a channel. Every flag is computed by the same resolver the refusing endpoint uses, so the two can never disagree.

It is read-only and does not require the Send Message module — call it first to decide what to show or attempt.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. It is read-only and does not require the Send Message module.

## Request Parameters

This endpoint takes no parameters. Send an empty JSON object: `{}`.

## Request Example

```json
{}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/usage" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/usage', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/messaging/usage"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {}

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
    "ModuleEnabled": true,
    "DirectMessageEnabled": true,
    "Email": {
      "IncludedInPlan": true,
      "Paused": false,
      "PausedBy": null,
      "Month": "2026-09",
      "MaxPerMonth": 5000,
      "Unlimited": false,
      "Used": 1240,
      "Reserved": 300,
      "Remaining": 3460,
      "LimitReached": false,
      "TransactionalCeiling": 6000
    },
    "Sms": {
      "IncludedInPlan": true,
      "Paused": false,
      "PausedBy": null,
      "Month": "2026-09",
      "MaxPerMonth": 1000,
      "Unlimited": false,
      "Used": 88,
      "Reserved": 0,
      "Remaining": 912,
      "LimitReached": false,
      "TransactionalCeiling": 1200,
      "SenderReady": true
    }
  },
  "Message": "Messaging usage fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `ModuleEnabled` | Boolean | The Send Message module is enabled. Every write endpoint of this category requires it |
| `DirectMessageEnabled` | Boolean | Direct messages through [Send Message to Participant](send.md) are enabled for the account |
| `Email / Sms . IncludedInPlan` | Boolean | The plan includes the channel |
| `Email / Sms . Paused` | Boolean | Sending on the channel is paused by Sweeppea |
| `Email / Sms . PausedBy` | String \| null | `platform` (all accounts) or `account` (this account); `null` when not paused |
| `Email / Sms . Month` | String | `YYYY-MM` — the account's calendar month in its own timezone |
| `Email / Sms . MaxPerMonth` | Number | Monthly allowance. `0` means unlimited |
| `Email / Sms . Unlimited` | Boolean | `true` when `MaxPerMonth` is `0` |
| `Email / Sms . Used` | Number | Units already sent this month |
| `Email / Sms . Reserved` | Number | Units held by launched campaigns that have not finished |
| `Email / Sms . Remaining` | Number \| null | `MaxPerMonth - Used - Reserved` (never below 0); `null` when unlimited |
| `Email / Sms . LimitReached` | Boolean | `Used + Reserved` has reached `MaxPerMonth` |
| `Email / Sms . TransactionalCeiling` | Number \| null | 120% of `MaxPerMonth` — transactional messages (such as direct messages) keep flowing up to this ceiling; `null` when unlimited |
| `Sms.SenderReady` | Boolean | A registered and validated 10DLC number is on the account — required for any SMS |

## Error Responses

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

- Read-only: it works even when the Send Message module is disabled, so an integration can find out why its writes are refused.
- A plan without a `MaxEmailsPerMonth` / `MaxSmsPerMonth` value uses the platform default of 1,000 per month — a missing value is never treated as unlimited.
- `Reserved` units are released when a campaign finishes or is cancelled with [Cancel Campaign](cancel-campaign.md).
- All six reads (module, two allowances, 10DLC, two pause checks) run in parallel.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
