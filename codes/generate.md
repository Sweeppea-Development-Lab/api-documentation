# Generate Codes

Generate up to 5,000 unique random codes in a sweepstakes.

## Endpoint

`POST /codes/generate`

## Description

This endpoint creates codes for you. Each code is `Prefix` + `Length` random characters + `Suffix`, drawn with a cryptographically secure generator — a code is a prize, and a predictable one is a stolen one. Codes the sweepstakes already holds are never produced twice.

Before generating, the endpoint checks the space: the sweepstakes' existing codes plus the new ones may use at most **half** of the combinations the chosen length and character set allow. Past that, generation would degrade into guessing and short codes would become easy to brute-force at a redemption counter, so the request is refused with `InsufficientSpace` — increase `Length`.

> **⚠️ Ceilings** — Up to 1,000 codes per `/codes/create` call and 5,000 per `/codes/generate` call · 50,000 new codes per sweepstakes per rolling 24 hours, create and generate combined · 1,000,000 codes per sweepstakes. For larger batches use Codes & Coupons → Import in the Sweeppea app.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Codes & Coupons module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | Yes | UUID v4 of a sweepstakes of your account |
| `Quantity` | Number | Yes | Whole number, `1` to `5000` |
| `Length` | Number | Yes | Random characters per code, `4` to `20`. `Prefix` + `Length` + `Suffix` must not exceed 20 |
| `Type` | String | No | `alphanumeric` or `numeric`. Defaults to `alphanumeric` |
| `Case` | String | No | `upper`, `lower` or `mixed` (alphanumeric only). Defaults to `upper` |
| `Prefix` | String | No | Letters, digits, `-` or `_` placed before every code |
| `Suffix` | String | No | Letters, digits, `-` or `_` placed after every code |
| `Description` | String | No | Applied to every code, max 5,000 characters |
| `Value` | String | No | Applied to every code, max 10 characters (e.g. `$10`, `15%`). A number is accepted too |
| `ExternalURL` | String | No | `https://` link applied to every code, max 500 characters |
| `ExpirationDate` | String | No | `YYYY-MM-DD`, not in the past |
| `ExpirationTime` | String | No | `HH:mm`, 24-hour clock |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "Quantity": 3,
  "Length": 8,
  "Type": "alphanumeric",
  "Case": "upper",
  "Prefix": "FALL-",
  "Value": "15%"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/codes/generate" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SweepstakesToken": "uuid-v4-string",
        "Quantity": 3,
        "Length": 8,
        "Type": "alphanumeric",
        "Case": "upper",
        "Prefix": "FALL-",
        "Value": "15%"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/generate', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "uuid-v4-string",
    Quantity: 3,
    Length: 8,
    Type: "alphanumeric",
    Case: "upper",
    Prefix: "FALL-",
    Value: "15%"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/codes/generate"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "Quantity": 3,
    "Length": 8,
    "Type": "alphanumeric",
    "Case": "upper",
    "Prefix": "FALL-",
    "Value": "15%"
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
    "Generated": 3,
    "Codes": [
      {
        "CouponToken": "uuid-v4-string",
        "CouponCode": "FALL-7QK2P9XA"
      },
      {
        "CouponToken": "uuid-v4-string",
        "CouponCode": "FALL-M3T8ZC1R"
      },
      {
        "CouponToken": "uuid-v4-string",
        "CouponCode": "FALL-0HW5JD6N"
      }
    ]
  },
  "Message": "3 code(s) generated"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `SweepstakesToken` | String | The sweepstakes |
| `Generated` | Number | Codes created (always equal to `Quantity` on success) |
| `Codes` | Array | `{ CouponToken, CouponCode }` of every generated code |

### Character Sets

| Type / Case | Characters | Combinations for Length 8 |
|---|---|---|
| `numeric` | `0-9` (10) | 100,000,000 |
| `alphanumeric` + `upper` | `0-9 A-Z` (36) | ≈ 2.8 trillion |
| `alphanumeric` + `lower` | `0-9 a-z` (36) | ≈ 2.8 trillion |
| `alphanumeric` + `mixed` | `0-9 a-z A-Z` (62) | ≈ 218 trillion |

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
      "Quantity": "number (required) — 1 to 5000",
      "Length": "number (required) — Random characters per code, 4 to 20 (Prefix + Length + Suffix must not exceed 20)",
      "Type": "string (optional) — alphanumeric | numeric. Defaults to alphanumeric",
      "Case": "string (optional) — upper | lower | mixed (alphanumeric only). Defaults to upper",
      "Prefix": "string (optional) — Letters, numbers, hyphens or underscores placed before every code",
      "Suffix": "string (optional) — Letters, numbers, hyphens or underscores placed after every code",
      "Description": "string (optional) — Applied to every code, max 5000 characters",
      "Value": "string (optional) — Applied to every code, max 10 characters",
      "ExternalURL": "string (optional) — https:// link applied to every code",
      "ExpirationDate": "string (optional) — YYYY-MM-DD",
      "ExpirationTime": "string (optional) — HH:mm (24-hour)",
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
  "Message": "Invalid Length. It must be a whole number from 4 to 20.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) — UUID v4 of a sweepstakes of your account",
      "Quantity": "number (required) — 1 to 5000",
      "Length": "number (required) — Random characters per code, 4 to 20 (Prefix + Length + Suffix must not exceed 20)",
      "Type": "string (optional) — alphanumeric | numeric. Defaults to alphanumeric",
      "Case": "string (optional) — upper | lower | mixed (alphanumeric only). Defaults to upper",
      "Prefix": "string (optional) — Letters, numbers, hyphens or underscores placed before every code",
      "Suffix": "string (optional) — Letters, numbers, hyphens or underscores placed after every code",
      "Description": "string (optional) — Applied to every code, max 5000 characters",
      "Value": "string (optional) — Applied to every code, max 10 characters",
      "ExternalURL": "string (optional) — https:// link applied to every code",
      "ExpirationDate": "string (optional) — YYYY-MM-DD",
      "ExpirationTime": "string (optional) — HH:mm (24-hour)",
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
  "Message": "Not enough room: 4-character numeric codes allow 10,000 combinations, and 6,000 codes would use more than half of them. Increase Length.",
  "Code": 422,
  "Data": {
    "Code": "InsufficientSpace"
  }
}
```

**429 Too Many Requests**

```json
{
  "Response": false,
  "Message": "Daily limit reached: at most 50,000 new codes per sweepstakes per 24 hours (48,000 already added).",
  "Code": 429,
  "Data": {
    "Code": "DailyLimit",
    "AddedToday": 48000
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
- Whitespace in `Prefix` and `Suffix` is removed.
- `Case` has no effect on `numeric` codes.
- The generator never loads the sweepstakes' existing codes; it draws candidates and checks them against the index in batches, so it works the same for 100 codes or a million.
- The generation is all or nothing: either `Quantity` codes are created or none (`422 InsufficientSpace`).
- Writes an entry to the account log at level 3, prefixed `[ API v3 ]`.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
