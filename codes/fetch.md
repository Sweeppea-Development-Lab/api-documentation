# Fetch Codes

One page of a sweepstakes' codes, filtered, searched and sorted on the server.

## Endpoint

`POST /codes/fetch`

## Description

This endpoint returns the codes of one sweepstakes a page at a time. Filter by state, search by part of the code or by the email or phone of the participant it is assigned to, and sort by one of five columns. Each code carries `AssignedTo` — the email (or phone) of its participant.

Without `SortBy` the newest codes come first. Every sort uses a unique tiebreaker, so paging never shows the same code twice or skips one, even for imported codes that share a creation time.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. It is read-only and does not require the Codes & Coupons module.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | Yes | UUID v4 of a sweepstakes of your account |
| `Filter` | String | No | `all`, `assigned`, `unassigned`, `redeemed`, `not-redeemed`, `voided`, `available`. Defaults to `all`. `available` = not redeemed and not assigned |
| `Search` | String | No | Part of a code, or of the email / phone of the assigned participant (case-insensitive, max 100 characters) |
| `SortBy` | String | No | `CouponCode`, `ExpirationDate`, `Status`, `CreationDate`, `RedemptionDate`. Defaults to `CreationDate`, newest first |
| `SortDesc` | Boolean | No | `true` for descending order (applies with `SortBy`) |
| `Page` | Number | No | Page number, starting at 1. Defaults to `1` |
| `ItemsPerPage` | Number | No | `1` to `500`. Defaults to `25` |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "Filter": "assigned",
  "Page": 1,
  "ItemsPerPage": 25
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/codes/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SweepstakesToken": "uuid-v4-string",
        "Filter": "assigned",
        "Page": 1,
        "ItemsPerPage": 25
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/fetch', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "uuid-v4-string",
    Filter: "assigned",
    Page: 1,
    ItemsPerPage: 25
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/codes/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "Filter": "assigned",
    "Page": 1,
    "ItemsPerPage": 25
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
    "Codes": [
      {
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
    ],
    "TotalResults": 1320,
    "Page": 1,
    "ItemsPerPage": 25,
    "TotalPages": 53,
    "SearchTruncated": false
  },
  "Message": "Codes fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Codes` | Array | The page of codes. Each item has the fields below |
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
| `TotalResults / Page / ItemsPerPage / TotalPages` | Number | Pagination (the values actually applied) |
| `SearchTruncated` | Boolean | `true` when the search matched more than 2,000 participants and only the first 2,000 were considered |

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
      "Filter": "string (optional) — all | assigned | unassigned | redeemed | not-redeemed | voided | available. Defaults to all. \"available\" = not redeemed and not assigned",
      "Search": "string (optional) — Part of a code, or of the email / phone of the participant it is assigned to. Max 100 characters",
      "SortBy": "string (optional) — CouponCode | ExpirationDate | Status | CreationDate | RedemptionDate. Defaults to CreationDate, newest first",
      "SortDesc": "boolean (optional) — true for descending order",
      "Page": "number (optional) — Page number, starting at 1. Defaults to 1",
      "ItemsPerPage": "number (optional) — 1 to 500. Defaults to 25",
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
  "Message": "Invalid Filter. Accepted values: all, assigned, unassigned, redeemed, not-redeemed, voided, available.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) — UUID v4 of a sweepstakes of your account",
      "Filter": "string (optional) — all | assigned | unassigned | redeemed | not-redeemed | voided | available. Defaults to all. \"available\" = not redeemed and not assigned",
      "Search": "string (optional) — Part of a code, or of the email / phone of the participant it is assigned to. Max 100 characters",
      "SortBy": "string (optional) — CouponCode | ExpirationDate | Status | CreationDate | RedemptionDate. Defaults to CreationDate, newest first",
      "SortDesc": "boolean (optional) — true for descending order",
      "Page": "number (optional) — Page number, starting at 1. Defaults to 1",
      "ItemsPerPage": "number (optional) — 1 to 500. Defaults to 25",
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
- The search is a literal (regular-expression characters are escaped). Phone numbers are matched on their digits when the search holds at least 3.
- `Page` and `ItemsPerPage` out of range are clamped (`ItemsPerPage` to 1–500), not rejected.
- `AssignedTo` is resolved after pagination, for the returned page only.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
