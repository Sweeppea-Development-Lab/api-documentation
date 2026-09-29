# Delete Codes

Delete up to 1,000 codes that nobody holds yet.

## Endpoint

`POST /codes/delete`

## Description

This endpoint deletes codes that are **unassigned and not redeemed**. Every other code in the list is reported in `Skipped` and left untouched: an assigned code is a participant's prize, and a redeemed or voided code is history your own reports depend on. Unknown tokens and codes of other accounts are skipped too.

> **🔴 Stays in the app** — Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file are done in the Sweeppea app, behind its confirmations — not through the API.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Codes & Coupons module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CouponTokens` | Array | Yes | 1 to 1,000 UUID v4 code tokens (duplicates are collapsed) |

## Request Example

```json
{
  "CouponTokens": [
    "uuid-v4-string",
    "uuid-v4-string"
  ]
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/codes/delete" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "CouponTokens": [
            "uuid-v4-string",
            "uuid-v4-string"
        ]
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/delete', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    CouponTokens: [
      "uuid-v4-string",
      "uuid-v4-string"
    ]
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/codes/delete"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "CouponTokens": [
        "uuid-v4-string",
        "uuid-v4-string"
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
    "Requested": 2,
    "Deleted": 1,
    "Skipped": [
      "uuid-v4-string"
    ],
    "SkippedCount": 1
  },
  "Message": "1 code(s) deleted"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Requested` | Number | Distinct tokens received |
| `Deleted` | Number | Codes deleted |
| `Skipped / SkippedCount` | Array / Number | Tokens not deleted (assigned, redeemed, voided, unknown or of another account) and their total |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: CouponTokens (a non-empty array)",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CouponTokens": "array (required) — 1 to 1000 UUID v4 code tokens. Only unassigned, not-redeemed codes are deleted",
      "CalledFromMCP": "boolean (optional) — Set by the MCP server, ignore it",
      "CalledFromCLI": "boolean (optional) — Set by the CLI, ignore it"
    }
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
- Deletion cannot be undone.
- To free an assigned code first, call [Unassign Code](unassign.md).
- Writes an entry to the account log at level 2, prefixed `[ API v3 ]`, when at least one code is deleted.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
