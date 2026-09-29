# Send Code to Participant

Send an assigned code to the participant who holds it, by email, SMS or both.

## Endpoint

`POST /codes/send`

## Description

This endpoint is the API twin of the "Send code" action in the Sweeppea app. **There is no address field**: the code goes to the participant it is assigned to, at the email address and/or phone number they registered with, and only when it is that participant's **current** code.

Nothing is sent from this endpoint. The message is queued and the Messaging Engine writes it in the sweepstakes' language and delivers it within about a minute, re-checking suppressions, the monthly allowance, sending pauses and, for SMS, the 10DLC number and quiet hours (an SMS during quiet hours is deferred, never dropped).

> **⚠️ Enabled per account** — Sending codes through the API requires the Messaging Engine rollout to be enabled for the account. Until then this endpoint answers `403` with `Data.Code: "DirectMessageNotEnabled"` — contact Sweeppea support. There is no fallback path around the engine.

> **Limits** — 200 successful sends per API key per rolling 24 hours · 3 code messages per recipient address per 24 hours · for SMS, 3 messages of any kind per number per 24 hours · US and Canadian numbers only · a validated 10DLC number is required for SMS.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Codes & Coupons module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CouponToken` | String | Yes | UUID v4 of a code assigned to a participant |
| `Channel` | String | Yes | `email`, `sms` or `both`. `both` sends on each channel the participant has a usable address for |
| `IdempotencyKey` | String | No | 8 to 64 characters `[A-Za-z0-9_-]`. Retrying with the same key never sends twice |

## Request Example

```json
{
  "CouponToken": "uuid-v4-string",
  "Channel": "both",
  "IdempotencyKey": "pos-send-000913"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/codes/send" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "CouponToken": "uuid-v4-string",
        "Channel": "both",
        "IdempotencyKey": "pos-send-000913"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/send', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    CouponToken: "uuid-v4-string",
    Channel: "both",
    IdempotencyKey: "pos-send-000913"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/codes/send"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "CouponToken": "uuid-v4-string",
    "Channel": "both",
    "IdempotencyKey": "pos-send-000913"
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
    "CouponToken": "uuid-v4-string",
    "ParticipantToken": "uuid-v4-string",
    "Duplicate": false,
    "Channels": [
      {
        "Channel": "email",
        "Status": "queued",
        "MessageToken": "uuid-v4-string"
      },
      {
        "Channel": "sms",
        "Status": "skipped",
        "Reason": "NoAddress",
        "Message": "The participant has no valid phone number."
      }
    ]
  },
  "Message": "Code queued. It is delivered within about a minute (SMS outside quiet hours)."
}
```

**200 OK — Duplicate IdempotencyKey**

```json
{
  "Response": true,
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 312,
    "MaxAPICalls": 1500000
  },
  "Data": {
    "CouponToken": "uuid-v4-string",
    "Duplicate": true,
    "Channels": [
      {
        "Channel": "email",
        "Status": "delivered",
        "MessageToken": "uuid-v4-string"
      }
    ]
  },
  "Message": "This code was already queued with the same IdempotencyKey. Nothing new was sent."
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `CouponToken` | String | The code |
| `ParticipantToken` | String | The participant it was queued to (absent on a duplicate) |
| `Duplicate` | Boolean | `true` when the same `IdempotencyKey` was already used for this code — nothing new was sent |
| `Channels[].Status` | String | `queued` for a new message (on a duplicate, the current delivery status), or `skipped` |
| `Channels[].MessageToken` | String | Id of the queued message |
| `Channels[].Reason / Message` | String | Why a channel was skipped: `NoAddress`, `UnsupportedCountry`, `Suppressed`, `RecipientDailyLimit`, `AllowanceExhausted` |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: Channel",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CouponToken": "string (required) — UUID v4 of a code assigned to a participant",
      "Channel": "string (required) — email | sms | both",
      "IdempotencyKey": "string (optional) — 8 to 64 characters [A-Za-z0-9_-]. Retrying with the same key never sends twice",
      "CalledFromMCP": "boolean (optional) — Set by the MCP server, ignore it",
      "CalledFromCLI": "boolean (optional) — Set by the CLI, ignore it"
    }
  }
}
```

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "Sending codes through the API is not enabled for your account yet. Contact Sweeppea support to enable it.",
  "Code": 403,
  "Data": {
    "Code": "DirectMessageNotEnabled"
  }
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Code not found. It must exist and belong to your account.",
  "Code": 404
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This code is not assigned to any participant. Assign it first with /codes/assign.",
  "Code": 409,
  "Data": {
    "Code": "NotAssigned"
  }
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This is not the participant's current code, so it cannot be sent (the message always carries the participant's current code).",
  "Code": 409,
  "Data": {
    "Code": "NotCurrentCode"
  }
}
```

**422 Unprocessable Entity**

```json
{
  "Response": false,
  "Message": "Nothing was sent. See Data.Channels for the reason on each channel.",
  "Code": 422,
  "Data": {
    "Channels": [
      {
        "Channel": "email",
        "Status": "skipped",
        "Reason": "Suppressed",
        "Message": "This address is on the suppression list."
      }
    ]
  }
}
```

**429 Too Many Requests**

```json
{
  "Response": false,
  "Message": "Daily limit reached: at most 200 code messages per 24 hours through the API.",
  "Code": 429,
  "Data": {
    "Code": "AccountDailyLimit",
    "Limit": 200
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
  "Message": "The Codes & Coupons module is not enabled for your account. Contact support to enable it.",
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

- Requires the Codes & Coupons module on the account **and** the Messaging Engine rollout (`403` otherwise).
- The message text is written by the Messaging Engine from the sweepstakes' own settings, in the sweepstakes' language — the API does not take a message.
- Codes are sent as **transactional** messages: they count against the channel's monthly allowance and may use it up to its 120% transactional ceiling.
- `IdempotencyKey` is scoped to the code, participant and channel; a retry with the same key returns `Duplicate: true` and does not count toward the daily ceiling.
- With `Channel: "both"`, channels without a usable address are reported as `skipped` while the others are still queued.
- Writes an entry to the account log at level 3, prefixed `[ API v3 ]`.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
