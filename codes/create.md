# Create Codes

Add up to 1,000 codes you already have — from a POS, a CRM or a printed batch — to a sweepstakes.

## Endpoint

`POST /codes/create`

## Description

This endpoint inserts a batch of existing codes into one sweepstakes. Each code must follow the platform rule — up to 20 letters, digits, `-` or `_`; whitespace is removed; matched exactly and case-sensitively. A code that breaks it is reported in `Invalid`, never silently rewritten. A code the sweepstakes already holds, or one repeated in the batch, is reported in `Duplicates` and not inserted: two rows with one code would hand the same prize out twice.

The optional attributes (`Description`, `Value`, `ExternalURL`, `ExpirationDate`, `ExpirationTime`) apply to every code of the batch. New codes start unassigned with status `not-redeemed`. To let Sweeppea invent the codes, use [Generate Codes](generate.md).

> **⚠️ Ceilings** — Up to 1,000 codes per `/codes/create` call and 5,000 per `/codes/generate` call · 50,000 new codes per sweepstakes per rolling 24 hours, create and generate combined · 1,000,000 codes per sweepstakes. For larger batches use Codes & Coupons → Import in the Sweeppea app.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Codes & Coupons module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | Yes | UUID v4 of a sweepstakes of your account |
| `Codes` | Array | Yes | 1 to 1,000 codes — up to 20 letters, digits, `-` or `_`; whitespace is removed; matched exactly and case-sensitively |
| `Description` | String | No | Applied to every code, max 5,000 characters |
| `Value` | String | No | Applied to every code, max 10 characters (e.g. `$10`, `15%`). A number is accepted too |
| `ExternalURL` | String | No | `https://` link applied to every code, max 500 characters |
| `ExpirationDate` | String | No | `YYYY-MM-DD`, not in the past |
| `ExpirationTime` | String | No | `HH:mm`, 24-hour clock |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "Codes": [
    "STORE-0001",
    "STORE-0002",
    "STORE-0002",
    "bad code!"
  ],
  "Value": "$10",
  "ExpirationDate": "2026-12-31"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/codes/create" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SweepstakesToken": "uuid-v4-string",
        "Codes": [
            "STORE-0001",
            "STORE-0002",
            "STORE-0002",
            "bad code!"
        ],
        "Value": "$10",
        "ExpirationDate": "2026-12-31"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/create', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "uuid-v4-string",
    Codes: [
      "STORE-0001",
      "STORE-0002",
      "STORE-0002",
      "bad code!"
    ],
    Value: "$10",
    ExpirationDate: "2026-12-31"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/codes/create"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "Codes": [
        "STORE-0001",
        "STORE-0002",
        "STORE-0002",
        "bad code!"
    ],
    "Value": "$10",
    "ExpirationDate": "2026-12-31"
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
    "Received": 4,
    "Created": 2,
    "Codes": [
      {
        "CouponToken": "uuid-v4-string",
        "CouponCode": "STORE-0001"
      },
      {
        "CouponToken": "uuid-v4-string",
        "CouponCode": "STORE-0002"
      }
    ],
    "Duplicates": [
      "STORE-0002"
    ],
    "DuplicateCount": 1,
    "Invalid": [
      "bad code!"
    ],
    "InvalidCount": 1
  },
  "Message": "2 code(s) created"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `SweepstakesToken` | String | The sweepstakes |
| `Received` | Number | Entries in `Codes` |
| `Created` | Number | Codes inserted |
| `Codes` | Array | `{ CouponToken, CouponCode }` of the created codes (the first 100) |
| `Duplicates / DuplicateCount` | Array / Number | Codes repeated in the batch or already in the sweepstakes (the first 100) and their total |
| `Invalid / InvalidCount` | Array / Number | Entries that break the code rule (the first 100, each cut to 40 characters) and their total |

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
      "Codes": "array (required) — 1 to 1000 codes. Each: up to 20 letters, numbers, hyphens or underscores",
      "Description": "string (optional) — Applied to every code, max 5000 characters",
      "Value": "string (optional) — Applied to every code, max 10 characters (e.g. \"$10\", \"15%\")",
      "ExternalURL": "string (optional) — https:// link applied to every code, max 500 characters",
      "ExpirationDate": "string (optional) — YYYY-MM-DD, applied to every code",
      "ExpirationTime": "string (optional) — HH:mm (24-hour), applied to every code",
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
  "Message": "Missing required parameter: Codes (a non-empty array)",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) — UUID v4 of a sweepstakes of your account",
      "Codes": "array (required) — 1 to 1000 codes. Each: up to 20 letters, numbers, hyphens or underscores",
      "Description": "string (optional) — Applied to every code, max 5000 characters",
      "Value": "string (optional) — Applied to every code, max 10 characters (e.g. \"$10\", \"15%\")",
      "ExternalURL": "string (optional) — https:// link applied to every code, max 500 characters",
      "ExpirationDate": "string (optional) — YYYY-MM-DD, applied to every code",
      "ExpirationTime": "string (optional) — HH:mm (24-hour), applied to every code",
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

**422 Unprocessable Entity**

```json
{
  "Response": false,
  "Message": "A sweepstakes can hold at most 1,000,000 codes (it has 999,800).",
  "Code": 422,
  "Data": {
    "Code": "SweepstakesLimit"
  }
}
```

**429 Too Many Requests**

```json
{
  "Response": false,
  "Message": "Daily limit reached: at most 50,000 new codes per sweepstakes per 24 hours (49,500 already added). For larger batches use Codes & Coupons → Import in the Sweeppea app.",
  "Code": 429,
  "Data": {
    "Code": "DailyLimit",
    "AddedToday": 49500
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
- Codes are case-sensitive: `ABC` and `abc` are two different codes.
- The ceilings are checked on the codes that would actually be inserted (after removing duplicates and invalid entries); a refused batch writes nothing.
- A daily-limit `429` carries `Data.Code: "DailyLimit"`, which tells it apart from the per-minute rate limit.
- Writes an entry to the account log at level 3, prefixed `[ API v3 ]`.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
