# Fetch Messaging Campaigns

List the account's email and SMS campaigns, newest first, filtered and paginated.

## Endpoint

`POST /messaging/campaigns`

## Description

This endpoint returns the campaigns of the account that owns the API key, sorted by creation date (newest first). Filter by sweepstakes, channel, category, status or part of the name. Archived campaigns are hidden unless `Archived: true` is sent, in which case only archived campaigns are listed.

Every campaign carries an `Actions` object saying what this API may do with it right now (pause, resume, cancel). Campaigns themselves are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. It is read-only and does not require the Send Message module.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | No | UUID v4. Only campaigns of this sweepstakes |
| `Channel` | String | No | `email` or `sms` |
| `Category` | String | No | `marketing`, `transactional` or `referral` |
| `Status` | String | No | `draft`, `scheduled`, `queued`, `sending`, `paused`, `completed`, `cancelled`, `failed`, `blocked` |
| `Search` | String | No | Part of the campaign name (case-insensitive, max 100 characters) |
| `Archived` | Boolean | No | `true` lists archived campaigns instead. Defaults to `false` |
| `Page` | Number | No | Page number, starting at 1. Defaults to `1` |
| `ItemsPerPage` | Number | No | `1` to `100`. Defaults to `25` |

## Request Example

```json
{
  "Channel": "email",
  "Status": "sending",
  "Page": 1,
  "ItemsPerPage": 25
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/campaigns" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "Channel": "email",
        "Status": "sending",
        "Page": 1,
        "ItemsPerPage": 25
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/campaigns', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    Channel: "email",
    Status: "sending",
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

url = "https://api-v3.sweeppea.com/messaging/campaigns"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "Channel": "email",
    "Status": "sending",
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
    "Campaigns": [
      {
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
      }
    ],
    "TotalResults": 1,
    "Page": 1,
    "ItemsPerPage": 25,
    "TotalPages": 1
  },
  "Message": "Campaigns fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Campaigns` | Array | The page of campaigns. Each item has the fields below |
| `CampaignToken` | String | UUID v4 of the campaign |
| `SweepstakesToken` | String \| null | The sweepstakes the campaign belongs to; `null` for an account-level campaign |
| `Name` | String | Campaign name |
| `Channel` | String | `email` or `sms` |
| `Category` | String | `marketing`, `transactional` or `referral` |
| `Status` | String | `draft`, `scheduled`, `queued`, `sending`, `paused`, `completed`, `cancelled`, `failed`, `blocked` |
| `BlockedReason` | String \| null | Why a `blocked` campaign was stopped |
| `ReputationHold` | Boolean | `true` when Sweeppea stopped the campaign to protect delivery (`HighBounceRate`, `HighComplaintRate` or `AccountSuspended`). Only Sweeppea support can release it |
| `Archived` | Boolean | Archived campaigns are hidden from the default list |
| `Origin` | String | `manual` for a campaign created by hand; otherwise the feature that created it (for example `agent`) |
| `Counters` | Object | `Targeted`, `Excluded` (by reason), `Queued`, `Sent`, `Delivered`, `Opened`, `Clicked`, `Bounced`, `Complained`, `Unsubscribed`, `Failed`, `Segments` |
| `QuotaReserved` | Number | Units of the monthly allowance the campaign is holding |
| `ReviewStatus` | String \| null | `pending`, `approved` or `rejected` when the campaign went through Sweeppea review |
| `ScheduledAt / ScheduleZone` | Date / String \| null | Scheduled send time and its timezone |
| `LaunchedAt / CompletedAt` | Date \| null | When sending started and finished |
| `CreationDate / UpdatedAt` | Date | Record timestamps |
| `Actions` | Object | What this API may do with the campaign right now: `Pause`, `Resume`, `Cancel` (booleans) |
| `TotalResults` | Number | Campaigns matching the filters |
| `Page / ItemsPerPage / TotalPages` | Number | Pagination (the values actually applied) |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid Channel. Accepted values: email, sms.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (optional) — UUID v4. Only campaigns of this sweepstakes (must belong to your account)",
      "Channel": "string (optional) — email | sms",
      "Category": "string (optional) — marketing | transactional | referral",
      "Status": "string (optional) — draft | scheduled | queued | sending | paused | completed | cancelled | failed | blocked",
      "Search": "string (optional) — Part of the campaign name, max 100 characters",
      "Archived": "boolean (optional) — true lists archived campaigns instead. Defaults to false",
      "Page": "number (optional) — Page number, starting at 1. Defaults to 1",
      "ItemsPerPage": "number (optional) — 1 to 100. Defaults to 25",
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
  "Message": "Invalid Status. Accepted values: draft, scheduled, queued, sending, paused, completed, cancelled, failed, blocked.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (optional) — UUID v4. Only campaigns of this sweepstakes (must belong to your account)",
      "Channel": "string (optional) — email | sms",
      "Category": "string (optional) — marketing | transactional | referral",
      "Status": "string (optional) — draft | scheduled | queued | sending | paused | completed | cancelled | failed | blocked",
      "Search": "string (optional) — Part of the campaign name, max 100 characters",
      "Archived": "boolean (optional) — true lists archived campaigns instead. Defaults to false",
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

- Ownership is part of the query: a `SweepstakesToken` of another account simply returns no campaigns.
- `Search` is matched as a literal (regular-expression characters are escaped) and is case-insensitive.
- `Page` and `ItemsPerPage` out of range are clamped (`ItemsPerPage` to 1–100), not rejected.
- `Archived` accepts `true` or the string `"true"`.
- Use [Fetch Single Campaign](campaign.md) for the content and audience, and [Campaign Report](campaign-report.md) for delivery statistics.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
