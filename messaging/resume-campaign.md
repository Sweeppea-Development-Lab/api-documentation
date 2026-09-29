# Resume Messaging Campaign

Resume a paused campaign whose recipient list was complete. Sending continues within about a minute.

## Endpoint

`POST /messaging/resume-campaign`

## Description

This endpoint resumes a campaign in status `paused` whose recipient list was fully built and whose allowance is still held. A resume here only ever means "carry on with the messages already queued": nothing is reserved again and no new recipient appears. The Messaging Engine picks the campaign up within about a minute.

Before flipping the status, everything that would only block the campaign again is re-checked — sending pauses, the plan's channel, the sender profile (From name, and the postal address for marketing email), the 10DLC number for SMS and prohibited content. **Every** finding is returned at once in `Data.Findings`, not just the first.

> **⚠️ Narrower than the app** — Blocked campaigns, and campaigns paused before their recipient list was complete, can only be resumed from the Sweeppea app, which shows what must be fixed and re-checks the sending schedule. A campaign stopped by Sweeppea to protect delivery (high bounce or complaint rate, or an account review) can only be released by Sweeppea support.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Send Message module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CampaignToken` | String | Yes | UUID v4 of a paused campaign |

## Request Example

```json
{
  "CampaignToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/resume-campaign" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "CampaignToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/resume-campaign', {
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

url = "https://api-v3.sweeppea.com/messaging/resume-campaign"
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
      "UpdatedAt": "2026-09-28T16:05:02.117Z",
      "Actions": {
        "Pause": true,
        "Resume": false,
        "Cancel": true
      }
    }
  },
  "Message": "Campaign resumed. Sending continues within about a minute."
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Campaign` | Object | The campaign after the resume (same fields as [Fetch Campaigns](campaigns.md)), `Status: "sending"` |

### Findings

A `422` refusal lists every problem in `Data.Findings` as `{ Code, Message }`. Possible codes:

| Code | Meaning |
|------|---------|
| `ChannelPaused` | Sweeppea has temporarily paused all sending on the channel |
| `AccountSuspended` | Sending on the channel is suspended on the account |
| `ModuleNotInPlan` | The plan does not include the channel |
| `NoSenderProfile` | The campaign has no sender profile |
| `NoFromName` | The email sender profile has no "From" name |
| `NoPhysicalAddress` | Marketing email requires the physical postal address on the sender profile |
| `NoRegisteredSmsSender` | SMS requires a registered and validated 10DLC number |
| `ProhibitedContent` | The content contains prohibited content |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: CampaignToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CampaignToken": "string (required) — UUID v4 of a paused campaign",
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
      "CampaignToken": "string (required) — UUID v4 of a paused campaign",
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
  "Message": "This campaign cannot be resumed through the API: its status is \"completed\". Only paused campaigns can be resumed here; resume blocked campaigns from the Sweeppea app, which shows what must be fixed first.",
  "Code": 409,
  "Data": {
    "Status": "completed",
    "BlockedReason": null
  }
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This campaign was stopped by Sweeppea to protect delivery (high bounce or complaint rate, or an account review). Only Sweeppea support can release it.",
  "Code": 409,
  "Data": {
    "Status": "blocked",
    "BlockedReason": "HighBounceRate"
  }
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This campaign was paused before its recipient list was complete. Resume it from the Sweeppea app, which re-checks the sending schedule before building the rest of the list.",
  "Code": 409,
  "Data": {
    "Status": "paused"
  }
}
```

**422 Unprocessable Entity**

```json
{
  "Response": false,
  "Message": "The campaign cannot be resumed until these problems are fixed.",
  "Code": 422,
  "Data": {
    "Findings": [
      {
        "Code": "NoPhysicalAddress",
        "Message": "Marketing email must carry your physical postal address. Add it to the sender profile."
      },
      {
        "Code": "ProhibitedContent",
        "Message": "This message cannot be sent: it contains prohibited content (fraud). Remove it and try again."
      }
    ]
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
- `Actions.Resume` in [Fetch Campaigns](campaigns.md) is `true` exactly when this endpoint can resume the campaign.
- The status flip is atomic and only matches a campaign that is still paused with its allowance held; a concurrent change answers `409` (`The campaign changed while it was being resumed…`).
- Refusals and successful resumes are written to the account log at level 2, prefixed `[ API v3 ]`.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
