# Fetch Code Statistics

How many codes a sweepstakes holds, and how many are assigned, redeemed, voided and still available.

## Endpoint

`POST /codes/stats`

## Description

This endpoint counts the codes of one sweepstakes in parallel on an index, without reading the codes themselves. `Available` is the number an integration usually needs: codes that are neither assigned to a participant nor redeemed or voided — the ones that can still be handed out.

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
curl -X POST "https://api-v3.sweeppea.com/codes/stats" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SweepstakesToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/stats', {
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

url = "https://api-v3.sweeppea.com/codes/stats"
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
    "Total": 5000,
    "Assigned": 1320,
    "Unassigned": 3680,
    "Redeemed": 410,
    "Voided": 12,
    "Available": 3668
  },
  "Message": "Code statistics fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `SweepstakesToken` | String | The sweepstakes |
| `Total` | Number | Every code of the sweepstakes |
| `Assigned` | Number | Codes assigned to a participant |
| `Unassigned` | Number | `Total - Assigned` |
| `Redeemed` | Number | Codes with status `redeemed` |
| `Voided` | Number | Codes with status `voided` |
| `Available` | Number | Codes with status `not-redeemed` and no participant |

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
- The five counts run in parallel.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
