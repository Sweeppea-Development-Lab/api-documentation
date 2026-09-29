# Fetch Messaging Campaign Report

The delivery report of one campaign: counters, rates, messages by status, top error codes, sends per hour and the most-clicked links.

## Endpoint

`POST /messaging/campaign-report`

## Description

This endpoint returns the same report the Sweeppea app shows for a campaign. `Rates` are percentages over **sent** messages with two decimals. The breakdowns are computed from the per-recipient rows, excluding test sends.

Per-recipient rows expire 180 days after they are created while the campaign counters are permanent. For an older campaign the breakdowns come back empty and `RowsExpired` is `true` — the totals in `Counters` and `Rates` are still accurate.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. It is read-only and does not require the Send Message module.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CampaignToken` | String | Yes | UUID v4 of the campaign |

## Request Example

```json
{
  "CampaignToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/campaign-report" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "CampaignToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/campaign-report', {
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

url = "https://api-v3.sweeppea.com/messaging/campaign-report"
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
      "Status": "sending",
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
      "UpdatedAt": "2026-09-28T15:12:48.300Z",
      "Actions": {
        "Pause": true,
        "Resume": false,
        "Cancel": true
      }
    },
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
    "Rates": {
      "Delivered": 99.05,
      "Opened": 40.74,
      "Clicked": 8.79,
      "Bounced": 0.61,
      "Complained": 0.04,
      "Unsubscribed": 0.26,
      "Failed": 0.35
    },
    "ByStatus": {
      "queued": 2331,
      "delivered": 1144,
      "opened": 738,
      "clicked": 203,
      "bounced": 14,
      "complained": 1,
      "unsubscribed": 6,
      "failed": 8,
      "sent": 196
    },
    "TopErrors": [
      {
        "Code": "MailboxDoesNotExist",
        "Count": 11
      },
      {
        "Code": "MessageRejected",
        "Count": 3
      }
    ],
    "Timeline": [
      {
        "Hour": "2026-09-28T15:00:00Z",
        "Count": 1400
      },
      {
        "Hour": "2026-09-28T16:00:00Z",
        "Count": 910
      }
    ],
    "TopLinks": [
      {
        "Link": "https://hub.sweeppea.com/f?tkn=uuid-v4-string",
        "Clicks": 187
      }
    ],
    "RowsExpired": false
  },
  "Message": "Campaign report fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Campaign` | Object | The campaign summary (same fields as [Fetch Campaigns](campaigns.md)) |
| `Counters` | Object | The campaign's permanent counters |
| `Rates` | Object | `Delivered`, `Opened`, `Clicked`, `Bounced`, `Complained`, `Unsubscribed`, `Failed` as percentages of `Counters.Sent` (two decimals; `0` when nothing was sent) |
| `ByStatus` | Object | Recipient rows per status (`queued`, `sent`, `delivered`, `opened`, …) |
| `TopErrors` | Array \| null | Up to 10 provider error codes of failed, bounced or suppressed messages, most frequent first |
| `Timeline` | Array \| null | Messages sent per hour (UTC), oldest first, up to 96 hours |
| `TopLinks` | Array \| null | Up to 10 most-clicked links |
| `RowsExpired` | Boolean | `true` when the recipient rows have expired (no rows left although messages were queued) |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: CampaignToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CampaignToken": "string (required) — UUID v4 of the campaign",
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
      "CampaignToken": "string (required) — UUID v4 of the campaign",
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

- `TopErrors`, `Timeline` and `TopLinks` are `null` (not an empty array) when that one breakdown could not be computed; the rest of the report still answers.
- The four aggregations run in parallel.
- Test sends are excluded from every breakdown.
- For the individual messages use [Campaign Recipients](campaign-recipients.md).
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
