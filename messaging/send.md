# Send Message to Participant

Queue one message you wrote to one of your own participants, by email, SMS or both.

## Endpoint

`POST /messaging/send`

## Description

This endpoint is the API twin of "Send message" on a participant profile in the Sweeppea app. **There is no address field**: the recipient is a `ParticipantToken` resolved inside your account (regular or AMOE entry), and the message goes to the email address and/or phone number that participant registered with. An API key can only ever reach its own participants.

**Nothing is sent from this endpoint.** The message is queued, and the Messaging Engine delivers it within about a minute, applying every check again at send time: suppressions, the monthly allowance, sending pauses, prohibited-content screening and, for SMS, the 10DLC number and quiet hours — an SMS during the recipient's quiet hours is deferred, never dropped. The message is also recorded in the participant's notification history.

Everything that can be known up front is refused up front, so an integration learns why instead of watching a message fail later.

> **⚠️ Enabled per account** — Direct messages through the API require the direct-message rollout to be enabled for the account (`DirectMessageEnabled` in [Messaging Usage](usage.md)). Until then this endpoint answers `403` with `Data.Code: "DirectMessageNotEnabled"` — contact Sweeppea support.

> **Limits** — 200 successful sends per API key per rolling 24 hours · 3 direct messages per recipient address per 24 hours · for SMS, 3 messages of any kind per number per 24 hours · US and Canadian numbers only · a validated 10DLC number is required for SMS.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Send Message module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `ParticipantToken` | String | Yes | UUID v4 of a participant (regular or AMOE) of your account |
| `Channel` | String | Yes | `email`, `sms` or `both`. `both` sends on each channel the participant has a usable address for |
| `Message` | String | Yes | Plain text. Max 5,000 characters (1,600 when sending by SMS) |
| `Subject` | String | No | Email subject, max 200 characters. Line breaks are collapsed. Defaults to a generic subject |
| `IdempotencyKey` | String | No | 8 to 64 characters `[A-Za-z0-9_-]`. Retrying with the same key never sends twice |

## Request Example

```json
{
  "ParticipantToken": "uuid-v4-string",
  "Channel": "both",
  "Subject": "About your entry",
  "Message": "Hi Jane, thanks for entering! Winners are announced on October 15.",
  "IdempotencyKey": "crm-msg-000481"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/send" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "ParticipantToken": "uuid-v4-string",
        "Channel": "both",
        "Subject": "About your entry",
        "Message": "Hi Jane, thanks for entering! Winners are announced on October 15.",
        "IdempotencyKey": "crm-msg-000481"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/send', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    ParticipantToken: "uuid-v4-string",
    Channel: "both",
    Subject: "About your entry",
    Message: "Hi Jane, thanks for entering! Winners are announced on October 15.",
    IdempotencyKey: "crm-msg-000481"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/messaging/send"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "ParticipantToken": "uuid-v4-string",
    "Channel": "both",
    "Subject": "About your entry",
    "Message": "Hi Jane, thanks for entering! Winners are announced on October 15.",
    "IdempotencyKey": "crm-msg-000481"
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
    "ParticipantToken": "uuid-v4-string",
    "NotificationId": "66f8a1c2e4b0a91d2c3e4f50",
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
  "Message": "Message queued. It is delivered within about a minute (SMS outside quiet hours)."
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
    "ParticipantToken": "uuid-v4-string",
    "NotificationId": "66f8a1c2e4b0a91d2c3e4f50",
    "Duplicate": true,
    "Channels": [
      {
        "Channel": "email",
        "Status": "delivered",
        "MessageToken": "uuid-v4-string"
      }
    ]
  },
  "Message": "This message was already queued with the same IdempotencyKey. Nothing new was sent."
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `ParticipantToken` | String | The recipient participant |
| `NotificationId` | String | Id of the entry added to the participant's notification history |
| `Duplicate` | Boolean | `true` when a message with the same `IdempotencyKey` was already queued — nothing new was sent |
| `Channels[]` | Array | One entry per requested channel |
| `Channels[].Status` | String | `queued` for a new message (on a duplicate, the current delivery status), or `skipped` |
| `Channels[].MessageToken` | String | Id of the queued message |
| `Channels[].Reason / Message` | String | Why a channel was skipped: `NoAddress`, `UnsupportedCountry`, `Suppressed`, `RecipientDailyLimit`, `AllowanceExhausted` |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: ParticipantToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "ParticipantToken": "string (required) — UUID v4 of a participant (regular or AMOE) of your account",
      "Channel": "string (required) — email | sms | both. \"both\" sends on each channel the participant has an address for",
      "Message": "string (required) — The message. Plain text, max 5000 characters (1600 for SMS)",
      "Subject": "string (optional) — Email subject, max 200 characters. Defaults to a generic subject",
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
  "Message": "Direct messages through the API are not enabled for your account yet. Contact Sweeppea support to enable them.",
  "Code": 403,
  "Data": {
    "Code": "DirectMessageNotEnabled"
  }
}
```

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "SMS requires a registered and validated 10DLC number on your account.",
  "Code": 403,
  "Data": {
    "Code": "NoRegisteredSmsSender"
  }
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Participant not found. The ParticipantToken must exist and belong to your account.",
  "Code": 404
}
```

**422 Unprocessable Entity**

```json
{
  "Response": false,
  "Message": "This message cannot be sent: it contains prohibited content (fraud). Remove it and try again.",
  "Code": 422,
  "Data": {
    "Code": "ProhibitedContent",
    "Categories": [
      "FRAUD"
    ]
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
        "Message": "This address is on the suppression list (it unsubscribed, replied STOP, complained, bounced or was added by you)."
      }
    ]
  }
}
```

**429 Too Many Requests**

```json
{
  "Response": false,
  "Message": "Daily limit reached: at most 200 direct messages per 24 hours through the API. Use a campaign in the Sweeppea app for bulk sends.",
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

- Requires the Send Message module on the account **and** the direct-message rollout (`403` otherwise).
- The recipient is always resolved inside your account; the address comes from the participant record, never from the request.
- Messages are plain text and sent as **transactional** messages. They count against the monthly allowance of the channel and may use it up to its 120% transactional ceiling (`TransactionalCeiling` in [Messaging Usage](usage.md)).
- Order of checks: input → direct-message rollout → participant → usable address per channel (`NoAddress`, `UnsupportedCountry` — non-US/CA numbers) → plan channel → sending pause → 10DLC → prohibited content → idempotency → daily ceilings, suppressions and allowance.
- `IdempotencyKey` is scoped to the participant and channel: a retry with the same key for the same participant returns `Duplicate: true` and the original messages; nothing new is sent and the retry does not count toward the daily ceiling.
- With `Channel: "both"`, channels without a usable address are reported as `skipped` while the others are still queued; `422` is returned only when nothing at all can be queued.
- For bulk sends create a campaign in the Sweeppea app — the API does not create campaigns.
- Writes an entry to the account log at level 3, prefixed `[ API v3 ]`.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
