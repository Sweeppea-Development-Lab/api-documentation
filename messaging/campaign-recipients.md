# Fetch Messaging Campaign Recipients

Who a campaign went to and what happened to each message, paginated.

## Endpoint

`POST /messaging/campaign-recipients`

## Description

This endpoint lists the recipient rows of a campaign in queue order: the address, the delivery status, the provider error code and the timestamps of each delivery event. Filter by status, or look up one exact email address or phone number.

The search is an **exact** address, compared through its hash so it can use an index — partial matches are not supported. An address that cannot be parsed matches nobody, never everybody.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. It is read-only and does not require the Send Message module.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CampaignToken` | String | Yes | UUID v4 of the campaign |
| `Status` | String | No | `queued`, `sending`, `sent`, `delivered`, `opened`, `clicked`, `bounced`, `complained`, `failed`, `suppressed`, `unsubscribed`, `cancelled` |
| `Search` | String | No | One exact email address or phone number (max 254 characters). Parsed for the campaign's channel |
| `Page` | Number | No | Page number, starting at 1. Defaults to `1` |
| `ItemsPerPage` | Number | No | `1` to `100`. Defaults to `25` |

## Request Example

```json
{
  "CampaignToken": "uuid-v4-string",
  "Status": "bounced",
  "Page": 1,
  "ItemsPerPage": 25
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/campaign-recipients" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "CampaignToken": "uuid-v4-string",
        "Status": "bounced",
        "Page": 1,
        "ItemsPerPage": 25
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/campaign-recipients', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    CampaignToken: "uuid-v4-string",
    Status: "bounced",
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

url = "https://api-v3.sweeppea.com/messaging/campaign-recipients"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "CampaignToken": "uuid-v4-string",
    "Status": "bounced",
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
    "Recipients": [
      {
        "Address": "jane.doe@example.com",
        "Status": "bounced",
        "ErrorCode": "MailboxDoesNotExist",
        "SentAt": "2026-09-28T15:02:11.410Z",
        "DeliveredAt": null,
        "OpenedAt": null,
        "ClickedAt": null,
        "NextAttemptAt": null,
        "Segments": 0
      }
    ],
    "TotalResults": 14,
    "Page": 1,
    "ItemsPerPage": 25,
    "TotalPages": 1
  },
  "Message": "Campaign recipients fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Recipients[].Address` | String | The email address or phone number (E.164) |
| `Recipients[].Status` | String | `queued`, `sending`, `sent`, `delivered`, `opened`, `clicked`, `bounced`, `complained`, `failed`, `suppressed`, `unsubscribed`, `cancelled` |
| `Recipients[].ErrorCode` | String \| null | Provider error code of a failed, bounced or suppressed message |
| `Recipients[].SentAt / DeliveredAt / OpenedAt / ClickedAt` | Date \| null | Delivery event timestamps |
| `Recipients[].NextAttemptAt` | Date \| null | When a deferred message (for example an SMS held by quiet hours) will be retried |
| `Recipients[].Segments` | Number | SMS segments billed for the message |
| `TotalResults / Page / ItemsPerPage / TotalPages` | Number | Pagination |

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
      "Status": "string (optional) — queued | sending | sent | delivered | opened | clicked | bounced | complained | failed | suppressed | unsubscribed | cancelled",
      "Search": "string (optional) — One exact email address or phone number",
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
  "Message": "Invalid CampaignToken. It must be a valid UUID v4.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CampaignToken": "string (required) — UUID v4 of the campaign",
      "Status": "string (optional) — queued | sending | sent | delivered | opened | clicked | bounced | complained | failed | suppressed | unsubscribed | cancelled",
      "Search": "string (optional) — One exact email address or phone number",
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
  "Message": "Invalid Status. Accepted values: queued, sending, sent, delivered, opened, clicked, bounced, complained, failed, suppressed, unsubscribed, cancelled.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CampaignToken": "string (required) — UUID v4 of the campaign",
      "Status": "string (optional) — queued | sending | sent | delivered | opened | clicked | bounced | complained | failed | suppressed | unsubscribed | cancelled",
      "Search": "string (optional) — One exact email address or phone number",
      "Page": "number (optional) — Page number, starting at 1. Defaults to 1",
      "ItemsPerPage": "number (optional) — 1 to 100. Defaults to 25",
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

- Test sends are never listed.
- Recipient rows expire 180 days after creation; after that this endpoint returns an empty list while [Campaign Report](campaign-report.md) still has the totals.
- Phone numbers are normalized to E.164 (`+1XXXXXXXXXX`) before the lookup, so `(305) 555-0147` and `+13055550147` find the same row.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
