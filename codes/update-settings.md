# Update Code Settings

Change how a sweepstakes hands out codes — only the keys you send change.

## Endpoint

`POST /codes/update-settings`

## Description

This endpoint updates the code settings of one sweepstakes. Only the keys present in the body change; the others keep their value. The first update creates the settings (one atomic upsert).

Every value is validated: the registration modes and code type are closed integer sets, the booleans must be real `true` / `false`, and the length is 4–20. The three texts are shown to the public on the entry and AMOE pages, so they are stored as **plain text**: angle brackets (`<`, `>`) and control characters are removed before the length is checked.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Codes & Coupons module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | Yes | UUID v4 of a sweepstakes of your account |
| `CouponEntryPageRegistration` | Number | No | Entry page: `0` none, `1` assign an existing code, `2` generate a new code, `3` ask the participant for their code |
| `CouponAmoePageRegistration` | Number | No | AMOE page: same values as `CouponEntryPageRegistration` |
| `WhatToDoWhenNoCouponCodeIsAvailable` | Number | No | `0` nothing, `1` generate a new code |
| `CouponCodeType` | Number | No | Generated codes: `1` numeric, `2` alphanumeric |
| `CouponCodeLength` | Number | No | Generated code length, `4` to `20` |
| `DisplayCouponCodeOnConfirmationPage` | Boolean | No | Show the code on the confirmation page |
| `SendCouponCodeToParticipantByEmail` | Boolean | No | Email the code to the participant |
| `SendCouponCodeToParticipantBySMS` | Boolean | No | Text the code to the participant |
| `RejectInvalidCouponCode` | Boolean | No | Mode 3: refuse the registration when the code is invalid |
| `CouponCodeCaseSensitive` | Boolean | No | Mode 3: compare codes case-sensitively |
| `InvalidCouponCodeErrorMessage` | String | null | No | Mode 3: message shown for an invalid code. Plain text, max 500 |
| `CouponRequestFieldLabel` | String | null | No | Mode 3: label of the code field. Plain text, max 200. Empty = default label |
| `CouponThankYouPageMessage` | String | null | No | Message on the thank-you page. Plain text, max 1,000 |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "CouponEntryPageRegistration": 1,
  "DisplayCouponCodeOnConfirmationPage": true,
  "SendCouponCodeToParticipantByEmail": true,
  "CouponThankYouPageMessage": "Show this code at any of our stores to claim your reward."
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/codes/update-settings" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SweepstakesToken": "uuid-v4-string",
        "CouponEntryPageRegistration": 1,
        "DisplayCouponCodeOnConfirmationPage": true,
        "SendCouponCodeToParticipantByEmail": true,
        "CouponThankYouPageMessage": "Show this code at any of our stores to claim your reward."
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/update-settings', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "uuid-v4-string",
    CouponEntryPageRegistration: 1,
    DisplayCouponCodeOnConfirmationPage: true,
    SendCouponCodeToParticipantByEmail: true,
    CouponThankYouPageMessage: "Show this code at any of our stores to claim your reward."
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/codes/update-settings"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "CouponEntryPageRegistration": 1,
    "DisplayCouponCodeOnConfirmationPage": True,
    "SendCouponCodeToParticipantByEmail": True,
    "CouponThankYouPageMessage": "Show this code at any of our stores to claim your reward."
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
    "SweepstakesToken": "uuid-v4-string",
    "Updated": [
      "CouponEntryPageRegistration",
      "DisplayCouponCodeOnConfirmationPage",
      "SendCouponCodeToParticipantByEmail",
      "CouponThankYouPageMessage"
    ],
    "Settings": {
      "CouponEntryPageRegistration": 1,
      "DisplayCouponCodeOnConfirmationPage": true,
      "SendCouponCodeToParticipantByEmail": true,
      "CouponThankYouPageMessage": "Show this code at any of our stores to claim your reward."
    }
  },
  "Message": "Code settings updated successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `SweepstakesToken` | String | The sweepstakes |
| `Updated` | Array | The keys changed by this call |
| `Settings` | Object | The stored settings document after the update — it holds only the keys ever saved, without defaults; read [Fetch Code Settings](settings.md) for the full set |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid CouponEntryPageRegistration. Accepted values: 0, 1, 2, 3.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) — UUID v4 of a sweepstakes of your account",
      "CouponEntryPageRegistration": "number (optional) — Entry page: 0 = none, 1 = assign an existing code, 2 = generate a new code, 3 = ask the participant for their code",
      "CouponAmoePageRegistration": "number (optional) — AMOE page: same values as CouponEntryPageRegistration",
      "WhatToDoWhenNoCouponCodeIsAvailable": "number (optional) — 0 = nothing, 1 = generate a new code",
      "CouponCodeType": "number (optional) — Generated codes: 1 = numeric, 2 = alphanumeric",
      "CouponCodeLength": "number (optional) — Generated code length, 4 to 20",
      "DisplayCouponCodeOnConfirmationPage": "boolean (optional)",
      "SendCouponCodeToParticipantByEmail": "boolean (optional)",
      "SendCouponCodeToParticipantBySMS": "boolean (optional)",
      "RejectInvalidCouponCode": "boolean (optional) — Mode 3: refuse the registration when the code is invalid",
      "CouponCodeCaseSensitive": "boolean (optional) — Mode 3: compare codes case-sensitively",
      "InvalidCouponCodeErrorMessage": "string (optional) — Plain text, max 500 characters",
      "CouponRequestFieldLabel": "string (optional) — Plain text, max 200 characters. Empty = default label",
      "CouponThankYouPageMessage": "string (optional) — Plain text, max 1000 characters",
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
  "Message": "Nothing to update. Send at least one setting.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) — UUID v4 of a sweepstakes of your account",
      "CouponEntryPageRegistration": "number (optional) — Entry page: 0 = none, 1 = assign an existing code, 2 = generate a new code, 3 = ask the participant for their code",
      "CouponAmoePageRegistration": "number (optional) — AMOE page: same values as CouponEntryPageRegistration",
      "WhatToDoWhenNoCouponCodeIsAvailable": "number (optional) — 0 = nothing, 1 = generate a new code",
      "CouponCodeType": "number (optional) — Generated codes: 1 = numeric, 2 = alphanumeric",
      "CouponCodeLength": "number (optional) — Generated code length, 4 to 20",
      "DisplayCouponCodeOnConfirmationPage": "boolean (optional)",
      "SendCouponCodeToParticipantByEmail": "boolean (optional)",
      "SendCouponCodeToParticipantBySMS": "boolean (optional)",
      "RejectInvalidCouponCode": "boolean (optional) — Mode 3: refuse the registration when the code is invalid",
      "CouponCodeCaseSensitive": "boolean (optional) — Mode 3: compare codes case-sensitively",
      "InvalidCouponCodeErrorMessage": "string (optional) — Plain text, max 500 characters",
      "CouponRequestFieldLabel": "string (optional) — Plain text, max 200 characters. Empty = default label",
      "CouponThankYouPageMessage": "string (optional) — Plain text, max 1000 characters",
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
  "Message": "Sweepstakes not found. The SweepstakesToken must exist and belong to your account.",
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

- Requires the Codes & Coupons module on the account (`403` otherwise).
- Numbers may be sent as numeric strings (`"1"`) but never as booleans; booleans must be JSON `true` / `false`.
- `null` clears a text setting.
- All validation happens before the sweepstakes is looked up, so a `400` never reveals whether a sweepstakes exists.
- Writes an entry to the account log at level 3, prefixed `[ API v3 ]`, naming the changed keys.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
