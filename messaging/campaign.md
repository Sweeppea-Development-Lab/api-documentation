# Fetch Single Messaging Campaign

Retrieve one campaign in full: content, audience, sender profile, counters and the actions this API may take next.

## Endpoint

`POST /messaging/campaign`

## Description

This endpoint returns everything the list returns for a campaign plus its `Content` (subject, preheader, SMS body and the structured content blocks), its `Audience` definition and the `Sender` profile it sends from. Internal fields (moderation notes, dispatch claims, the calendar reminder) are never exposed.

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
curl -X POST "https://api-v3.sweeppea.com/messaging/campaign" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "CampaignToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/campaign', {
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

url = "https://api-v3.sweeppea.com/messaging/campaign"
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
      },
      "Content": {
        "Subject": "Only 7 days left to enter",
        "Preheader": "Your chance to win ends next Sunday",
        "SmsBody": null,
        "Blocks": [
          {
            "Type": "heading",
            "Text": "The clock is ticking"
          },
          {
            "Type": "text",
            "Text": "Hi [ FIRST_NAME ], there is one week left to enter the giveaway."
          },
          {
            "Type": "button",
            "Label": "Enter now",
            "Url": "https://hub.sweeppea.com/f?tkn=uuid-v4-string"
          }
        ]
      },
      "Audience": {
        "Sources": [
          "participants"
        ],
        "Filters": {},
        "ExcludeRecentHours": 24
      },
      "Sender": {
        "ProfileToken": "uuid-v4-string"
      }
    }
  },
  "Message": "Campaign fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Campaign` | Object | Every field of [Fetch Campaigns](campaigns.md), plus: |
| `Content.Subject / Content.Preheader` | String \| null | Email subject and preheader |
| `Content.SmsBody` | String \| null | SMS text |
| `Content.Blocks` | Array | Structured email content (heading, text, button, image, divider, coupon, spacer). Merge fields such as `[ FIRST_NAME ]` are rendered at send time |
| `Audience` | Object | The audience definition (sources, filters, exclusions) |
| `Sender.ProfileToken` | String \| null | The sender profile the campaign sends from |

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

- Ownership is part of the lookup, so another account's campaign reads exactly like one that does not exist — `404`, never `403`.
- Campaign content is never supplied as HTML: blocks are rendered server-side at send time.
- Campaigns are created and edited in the Sweeppea app; this API reads them and can pause, resume or cancel them.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
