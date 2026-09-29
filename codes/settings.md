# Fetch Code Settings

How a sweepstakes hands out codes on its entry and AMOE pages, and what participants are shown and sent.

## Endpoint

`POST /codes/settings`

## Description

This endpoint returns the code settings of one sweepstakes. A read never writes: a sweepstakes that has never been configured answers the platform defaults with `Configured: false`, and only [Update Code Settings](update-settings.md) creates the settings.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. It is read-only and does not require the Codes & Coupons module.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | Yes | UUID v4 of a sweepstakes of your account |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/codes/settings" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SweepstakesToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/settings', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "uuid-v4-string"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/codes/settings"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string"
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
    "Configured": true,
    "Settings": {
      "CouponEntryPageRegistration": 1,
      "CouponAmoePageRegistration": 0,
      "WhatToDoWhenNoCouponCodeIsAvailable": 0,
      "CouponCodeType": 1,
      "CouponCodeLength": 20,
      "DisplayCouponCodeOnConfirmationPage": true,
      "SendCouponCodeToParticipantByEmail": true,
      "SendCouponCodeToParticipantBySMS": false,
      "RejectInvalidCouponCode": false,
      "InvalidCouponCodeErrorMessage": "",
      "CouponCodeCaseSensitive": false,
      "CouponRequestFieldLabel": "",
      "CouponThankYouPageMessage": "Show this code at any of our stores to claim your reward."
    }
  },
  "Message": "Code settings fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `SweepstakesToken` | String | The sweepstakes |
| `Configured` | Boolean | `false` when the sweepstakes has never been configured and the defaults are returned |
| `Settings.CouponEntryPageRegistration` | Number | Entry page: `0` none, `1` assign an existing code, `2` generate a new code, `3` ask the participant for their code (default `0`) |
| `Settings.CouponAmoePageRegistration` | Number | AMOE page: same values as `CouponEntryPageRegistration` (default `0`) |
| `Settings.WhatToDoWhenNoCouponCodeIsAvailable` | Number | `0` nothing, `1` generate a new code (default `0`) |
| `Settings.CouponCodeType` | Number | Generated codes: `1` numeric, `2` alphanumeric (default `1`) |
| `Settings.CouponCodeLength` | Number | Generated code length, `4` to `20` (default `20`) |
| `Settings.DisplayCouponCodeOnConfirmationPage` | Boolean | Show the code on the confirmation page (default `false`) |
| `Settings.SendCouponCodeToParticipantByEmail` | Boolean | Email the code to the participant (default `false`) |
| `Settings.SendCouponCodeToParticipantBySMS` | Boolean | Text the code to the participant (default `false`) |
| `Settings.RejectInvalidCouponCode` | Boolean | Mode 3: refuse the registration when the code is invalid (default `false`) |
| `Settings.CouponCodeCaseSensitive` | Boolean | Mode 3: compare codes case-sensitively (default `false`) |
| `Settings.InvalidCouponCodeErrorMessage` | String | Mode 3: message shown for an invalid code. Plain text, max 500 (default `""`) |
| `Settings.CouponRequestFieldLabel` | String | Mode 3: label of the code field. Plain text, max 200. Empty = default label (default `""`) |
| `Settings.CouponThankYouPageMessage` | String | Message on the thank-you page. Plain text, max 1,000 (default `""`) |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: SweepstakesToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) — UUID v4 of a sweepstakes of your account",
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

- Read-only: it does not require the Codes & Coupons module.
- Only the 13 documented keys are returned, whatever else the stored document holds.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
