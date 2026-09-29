# Add Messaging Suppressions

Add up to 500 addresses of one channel to the account's do-not-contact list in one call.

## Endpoint

`POST /messaging/add-suppressions`

## Description

The usual caller is a CRM syncing its own opt-outs: people who opted out there must stay opted out in Sweeppea, or the first campaign contacts them again. Every address is normalized first (lowercased email, E.164 phone); anything that cannot be normalized is not stored and is returned in `Invalid` so you can fix it.

The call is **idempotent**: sending the same batch twice adds nothing the second time. An address that is already suppressed keeps its original reason — an unsubscribe or a STOP is never downgraded to a removable `manual` row. New rows are stored with reason `manual` and apply to the whole account.

> **⚠️ Daily ceiling** — At most 20,000 addresses can be added through the API per rolling 24 hours. For larger lists use Send Message → Suppressions → Import in the Sweeppea app.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Send Message module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `Channel` | String | Yes | `email` or `sms` |
| `Addresses` | Array | Yes | 1 to 500 email addresses, or phone numbers (E.164 or 10-digit US/CA) |

## Request Example

```json
{
  "Channel": "email",
  "Addresses": [
    "jane.doe@example.com",
    "JOHN.SMITH@example.com",
    "not-an-email"
  ]
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/add-suppressions" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "Channel": "email",
        "Addresses": [
            "jane.doe@example.com",
            "JOHN.SMITH@example.com",
            "not-an-email"
        ]
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/add-suppressions', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    Channel: "email",
    Addresses: [
      "jane.doe@example.com",
      "JOHN.SMITH@example.com",
      "not-an-email"
    ]
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/messaging/add-suppressions"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "Channel": "email",
    "Addresses": [
        "jane.doe@example.com",
        "JOHN.SMITH@example.com",
        "not-an-email"
    ]
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
    "Channel": "email",
    "Received": 3,
    "Added": 1,
    "AlreadySuppressed": 1,
    "Invalid": [
      "not-an-email"
    ],
    "InvalidCount": 1
  },
  "Message": "1 address(es) added to the suppression list"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Channel` | String | The channel the addresses were added to |
| `Received` | Number | Entries in `Addresses` |
| `Added` | Number | New rows created |
| `AlreadySuppressed` | Number | Valid, distinct addresses that were already on the list (left untouched) |
| `Invalid` | Array | Entries that could not be normalized (the first 100) |
| `InvalidCount` | Number | Total entries that could not be normalized |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: Addresses (a non-empty array)",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "Channel": "string (required) — email | sms",
      "Addresses": "array (required) — 1 to 500 email addresses or phone numbers (E.164 or 10-digit US/CA)",
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
  "Message": "Too many addresses: at most 500 per call. Split the list into batches.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "Channel": "string (required) — email | sms",
      "Addresses": "array (required) — 1 to 500 email addresses or phone numbers (E.164 or 10-digit US/CA)",
      "CalledFromMCP": "boolean (optional) — Set by the MCP server, ignore it",
      "CalledFromCLI": "boolean (optional) — Set by the CLI, ignore it"
    }
  }
}
```

**429 Too Many Requests**

```json
{
  "Response": false,
  "Message": "Daily limit reached: at most 20000 addresses per 24 hours can be added through the API (19850 already added). For larger lists use Send Message → Suppressions → Import in the Sweeppea app.",
  "Code": 429,
  "Data": {
    "Code": "DailyLimit",
    "Limit": 20000,
    "AddedToday": 19850
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
- Duplicates inside one batch are collapsed before writing.
- Phone numbers must be US or Canadian (NANP): `+1XXXXXXXXXX`, `1XXXXXXXXXX` or 10 digits; formatting characters are ignored.
- The daily ceiling counts every valid address in the batch, including those already suppressed; the batch is refused whole (nothing is written) when it would pass the limit. A daily-limit `429` carries `Data.Code: "DailyLimit"`, which tells it apart from the per-minute rate limit.
- Writes an entry to the account log at level 3, prefixed `[ API v3 ]`, with the added, already-suppressed and invalid counts.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
