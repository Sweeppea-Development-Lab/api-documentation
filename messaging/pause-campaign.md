# Pause Messaging Campaign

Stop a campaign that is queued or sending. Nothing more goes out after this call answers.

## Endpoint

`POST /messaging/pause-campaign`

## Description

This endpoint pauses a campaign in status `queued` or `sending`. It is one atomic write: the allowed statuses are part of the update, so a campaign that finished a moment earlier simply does not match and answers `409`. The sender checks the campaign status before every chunk, so no further message is sent once this call returns.

The monthly allowance held by the campaign stays held: [Resume Campaign](resume-campaign.md) continues with it, [Cancel Campaign](cancel-campaign.md) releases it.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Send Message module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CampaignToken` | String | Yes | UUID v4 of a campaign in status `queued` or `sending` |

## Request Example

```json
{
  "CampaignToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/pause-campaign" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "CampaignToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/pause-campaign', {
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

url = "https://api-v3.sweeppea.com/messaging/pause-campaign"
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
    "Campaign": {
      "CampaignToken": "uuid-v4-string",
      "SweepstakesToken": "uuid-v4-string",
      "Name": "Final Week Reminder",
      "Channel": "email",
      "Category": "marketing",
      "Status": "paused",
      "BlockedReason": null,
      "ReputationHold": false,
      "Archived": false,
      "Origin": "manual",
      "Counters": {
        "Targeted": 4820,
        "Excluded": {
          "NoConsent": 112,
          "Suppressed": 37,
          "Invalid": 9,
          "Capped": 0,
          "Duplicate": 21,
          "NonUsCaNumber": 0,
          "NotFirstParty": 0,
          "OverQuota": 0
        },
        "Queued": 4641,
        "Sent": 2310,
        "Delivered": 2288,
        "Opened": 941,
        "Clicked": 203,
        "Bounced": 14,
        "Complained": 1,
        "Unsubscribed": 6,
        "Failed": 8,
        "Segments": 0
      },
      "QuotaReserved": 4641,
      "ReviewStatus": null,
      "ScheduledAt": null,
      "ScheduleZone": null,
      "LaunchedAt": "2026-09-28T15:00:04.112Z",
      "CompletedAt": null,
      "CreationDate": "2026-09-27T19:42:10.508Z",
      "UpdatedAt": "2026-09-28T15:20:31.902Z",
      "Actions": {
        "Pause": false,
        "Resume": true,
        "Cancel": true
      }
    }
  },
  "Message": "Campaign paused successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Campaign` | Object | The campaign after the pause (same fields as [Fetch Campaigns](campaigns.md)), `Status: "paused"` |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: CampaignToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CampaignToken": "string (required) — UUID v4 of a campaign in status queued or sending",
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
      "CampaignToken": "string (required) — UUID v4 of a campaign in status queued or sending",
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
  "Message": "This campaign cannot be paused: its status is \"completed\". Only queued or sending campaigns can be paused.",
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
- Writes an entry to the account log at level 2, prefixed `[ API v3 ]`, with the number of messages already sent.
- Messages already handed to the email or SMS provider before the pause are not recalled.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
