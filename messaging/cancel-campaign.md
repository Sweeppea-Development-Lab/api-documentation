# Cancel Messaging Campaign

Cancel a campaign that has not finished and give its held allowance back to the account.

## Endpoint

`POST /messaging/cancel-campaign`

## Description

This endpoint cancels a campaign in status `scheduled`, `queued`, `sending`, `paused` or `blocked`. The status flips first, in one atomic write; then the units of the monthly allowance the campaign was holding are released exactly once, the messages still queued are marked `cancelled`, and the campaign's calendar reminder (if any) is removed.

Messages already sent stay sent. A cancelled campaign cannot be resumed.

> **🔴 This cannot be undone** — A cancelled campaign never sends again. To stop a campaign temporarily use [Pause Campaign](pause-campaign.md). Campaigns stopped by Sweeppea to protect delivery cannot be cancelled through the API — only Sweeppea support can release or cancel them.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Send Message module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CampaignToken` | String | Yes | UUID v4 of a campaign in status `scheduled`, `queued`, `sending`, `paused` or `blocked` |

## Request Example

```json
{
  "CampaignToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/cancel-campaign" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "CampaignToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/cancel-campaign', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    CampaignToken: "uuid-v4-string"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/messaging/cancel-campaign"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "CampaignToken": "uuid-v4-string"
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
    "CampaignToken": "uuid-v4-string",
    "PreviousStatus": "paused",
    "Status": "cancelled",
    "Sent": 2310,
    "CancelledRows": 2331,
    "ReleasedUnits": 4641
  },
  "Message": "Campaign cancelled successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `CampaignToken` | String | The cancelled campaign |
| `PreviousStatus` | String | The status before the cancel |
| `Status` | String | Always `cancelled` |
| `Sent` | Number | Messages sent before the cancel |
| `CancelledRows` | Number | Queued messages marked `cancelled` |
| `ReleasedUnits` | Number | Units of the monthly allowance given back (`0` for a scheduled campaign, which never held any) |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: CampaignToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CampaignToken": "string (required) — UUID v4 of a campaign in status scheduled, queued, sending, paused or blocked",
      "CalledFromMCP": "boolean (optional) — Set by the MCP server, ignore it",
      "CalledFromCLI": "boolean (optional) — Set by the CLI, ignore it"
    }
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid CampaignToken. It must be a valid UUID v4.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CampaignToken": "string (required) — UUID v4 of a campaign in status scheduled, queued, sending, paused or blocked",
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
  "Message": "Campaign not found. The CampaignToken must exist and belong to your account.",
  "Code": 404
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This campaign was stopped by Sweeppea to protect delivery. Only Sweeppea support can release or cancel it.",
  "Code": 409,
  "Data": {
    "Status": "blocked",
    "BlockedReason": "HighComplaintRate"
  }
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This campaign cannot be cancelled: its status is \"completed\".",
  "Code": 409,
  "Data": {
    "Status": "completed"
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

- Requires the Send Message module on the account (`403` otherwise).
- Drafts are not cancelled — they have never been launched. Delete them in the Sweeppea app.
- The allowance is released in the month it was reserved in, never below zero, even when a cancel races the campaign's own completion.
- Marking the queued rows and removing the calendar reminder are best effort: the cancel already stands if either fails, and the sender skips a cancelled campaign regardless.
- Writes an entry to the account log at level 2, prefixed `[ API v3 ]`, with the units released.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
