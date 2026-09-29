# Fetch Single Code

Look up one code by its token, or by the code a customer presented within a sweepstakes.

## Endpoint

`POST /codes/single`

## Description

This endpoint returns one code with its status and the participant it is assigned to. Identify it either by `CouponToken`, or by `SweepstakesToken` + `CouponCode` — what a point-of-sale integration has when a customer shows a code. The code is matched exactly and case-sensitively, like the stored value (whitespace is removed first).

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. It is read-only and does not require the Codes & Coupons module.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CouponToken` | String | Conditional | UUID v4 of the code. Required unless `CouponCode` is sent; takes precedence when both are sent |
| `SweepstakesToken` | String | Conditional | UUID v4 of the sweepstakes. Required with `CouponCode` |
| `CouponCode` | String | No | The code itself — up to 20 letters, digits, `-` or `_`; whitespace is removed; matched exactly and case-sensitively |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "CouponCode": "SUMMER-7QK2P9"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/codes/single" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SweepstakesToken": "uuid-v4-string",
        "CouponCode": "SUMMER-7QK2P9"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/single', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "uuid-v4-string",
    CouponCode: "SUMMER-7QK2P9"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/codes/single"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "CouponCode": "SUMMER-7QK2P9"
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
    "Code": {
      "CouponToken": "uuid-v4-string",
      "SweepstakesToken": "uuid-v4-string",
      "CouponCode": "SUMMER-7QK2P9",
      "Description": "$10 off your next order",
      "Value": "$10",
      "ExternalURL": "https://shop.example.com/redeem",
      "ExpirationDate": "2026-12-31T00:00:00.000Z",
      "ExpirationTime": "23:59",
      "Status": "not-redeemed",
      "ParticipantToken": "uuid-v4-string",
      "AssignedTo": "jane.doe@example.com",
      "RedemptionDate": null,
      "RedemptionTime": null,
      "CreationDate": "2026-09-20T14:02:11.004Z"
    }
  },
  "Message": "Code fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Code` | Object | The code: |
| `CouponToken` | String | UUID v4 of the code |
| `SweepstakesToken` | String | The sweepstakes the code belongs to |
| `CouponCode` | String | The code itself |
| `Description / Value / ExternalURL` | String \| null | Optional attributes shown to the participant |
| `ExpirationDate` | Date \| null | Expiration day (stored at 00:00 UTC) |
| `ExpirationTime` | String \| null | Expiration time, `HH:mm` 24-hour |
| `Status` | String | `not-redeemed`, `redeemed` or `voided` |
| `ParticipantToken` | String \| null | The participant the code is assigned to |
| `AssignedTo` | String \| null | That participant's email (or phone when there is no email); `null` when unassigned |
| `RedemptionDate / RedemptionTime` | Date / String \| null | When the code was redeemed |
| `CreationDate` | Date | When the code was created |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: CouponToken (or SweepstakesToken + CouponCode)",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CouponToken": "string (required unless CouponCode is sent) — UUID v4 of the code",
      "SweepstakesToken": "string (required with CouponCode) — UUID v4 of the sweepstakes the code belongs to",
      "CouponCode": "string (optional) — The code itself, exact match, max 20 characters",
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
  "Message": "Missing or invalid SweepstakesToken. It is required when looking a code up by CouponCode.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CouponToken": "string (required unless CouponCode is sent) — UUID v4 of the code",
      "SweepstakesToken": "string (required with CouponCode) — UUID v4 of the sweepstakes the code belongs to",
      "CouponCode": "string (optional) — The code itself, exact match, max 20 characters",
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
  "Message": "Code not found. It must exist and belong to your account.",
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
- Ownership is part of the lookup: another account's code answers `404`, exactly like one that does not exist.
- To redeem a presented code in the same step, call [Redeem Code](redeem.md) with the same identifiers.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
