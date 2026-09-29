# Unassign Code

Take a code back from its participant and make it available again.

## Endpoint

`POST /codes/unassign`

## Description

This endpoint detaches a code from the participant who holds it and releases it completely: the participant no longer points at the code, and the code returns to status `not-redeemed` with its redemption date and time cleared, so it can be handed out again.

A voided code is never released — voiding is the decision that the code must never be handed out again.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Codes & Coupons module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CouponToken` | String | Yes | UUID v4 of an assigned code |

## Request Example

```json
{
  "CouponToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/codes/unassign" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "CouponToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/unassign', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    CouponToken: "uuid-v4-string"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/codes/unassign"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "CouponToken": "uuid-v4-string"
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
    "CouponCode": "SUMMER-7QK2P9",
    "PreviousParticipantToken": "uuid-v4-string",
    "Status": "not-redeemed"
  },
  "Message": "Code unassigned and available again"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `CouponToken / CouponCode` | String | The released code |
| `PreviousParticipantToken` | String | The participant who held it |
| `Status` | String | Always `not-redeemed` |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: CouponToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CouponToken": "string (required) — UUID v4 of an assigned code",
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
  "Message": "This code is not assigned to anybody.",
  "Code": 409,
  "Data": {
    "Status": "not-redeemed"
  }
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "A voided code cannot be released.",
  "Code": 409,
  "Data": {
    "Status": "voided"
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
- Unassigning a redeemed code also clears its redemption.
- Writes an entry to the account log at level 3, prefixed `[ API v3 ]`.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
