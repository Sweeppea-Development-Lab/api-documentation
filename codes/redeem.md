# Redeem Code

Mark one code as redeemed, exactly once — by token, or by the code a customer presents at a point of sale.

## Endpoint

`POST /codes/redeem`

## Description

This endpoint redeems one code. Identify it by `CouponToken`, or — at a register or checkout — by `SweepstakesToken` + the `CouponCode` the customer presented (exact, case-sensitive match; whitespace removed).

A code is redeemed **exactly once**. The check is part of the update itself, so two registers scanning the same code at the same second produce one redemption and one `409 AlreadyRedeemed` carrying the original redemption date and time. A voided code is never redeemable (`409 CodeVoided`).

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Codes & Coupons module must also be enabled for your account.

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
curl -X POST "https://api-v3.sweeppea.com/codes/redeem" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SweepstakesToken": "uuid-v4-string",
        "CouponCode": "SUMMER-7QK2P9"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/redeem', {
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

url = "https://api-v3.sweeppea.com/codes/redeem"
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
      "Status": "redeemed",
      "ParticipantToken": "uuid-v4-string",
      "AssignedTo": null,
      "RedemptionDate": "2026-09-29T15:04:22.118Z",
      "RedemptionTime": "15:04 pm",
      "CreationDate": "2026-09-20T14:02:11.004Z"
    }
  },
  "Message": "Code redeemed successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Code` | Object | The code after the redemption (same fields as [Fetch Single Code](single.md)), `Status: "redeemed"`. `AssignedTo` is `null` in this response. `RedemptionTime` uses the legacy `HH:mm am\|pm` format in UTC (24-hour hour) |

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
      "CouponCode": "string (optional) — The code itself, exact match",
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

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This code was already redeemed.",
  "Code": 409,
  "Data": {
    "Code": "AlreadyRedeemed",
    "RedemptionDate": "2026-09-28T19:41:05.330Z",
    "RedemptionTime": "19:41 pm"
  }
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This code was voided and cannot be redeemed.",
  "Code": 409,
  "Data": {
    "Code": "CodeVoided",
    "RedemptionDate": null,
    "RedemptionTime": null
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

- Requires the Codes & Coupons module on the account (`403` otherwise).
- Assigned and unassigned codes can both be redeemed; the assignment is kept.
- Treat `409 AlreadyRedeemed` as a definitive "do not honour" at a point of sale — it is also what a retried request returns after a successful redemption.
- A redemption made by mistake is reversed with [Unredeem Code](unredeem.md).
- Writes an entry to the account log at level 3, prefixed `[ API v3 ]`.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
