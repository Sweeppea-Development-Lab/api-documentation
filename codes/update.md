# Update Code

Change the attributes of one code — only the fields you send change.

## Endpoint

`POST /codes/update`

## Description

This endpoint updates one code. Only the fields present in the body are changed; send `null` (or an empty string) to clear an attribute.

Changing the code itself (`CouponCode`) is allowed only while the code is unassigned and not redeemed — a participant who was handed that exact string would otherwise hold a code that no longer exists — and never onto a code the sweepstakes already has.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Codes & Coupons module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CouponToken` | String | Yes | UUID v4 of the code |
| `CouponCode` | String | No | New code — up to 20 letters, digits, `-` or `_`; whitespace is removed; matched exactly and case-sensitively. Only while unassigned and not redeemed; unique in the sweepstakes |
| `Description` | String | null | No | Max 5,000 characters |
| `Value` | String | null | No | Max 10 characters |
| `ExternalURL` | String | null | No | `https://` link, max 500 characters |
| `ExpirationDate` | String | null | No | `YYYY-MM-DD`, not in the past |
| `ExpirationTime` | String | null | No | `HH:mm`, 24-hour clock |

## Request Example

```json
{
  "CouponToken": "uuid-v4-string",
  "Value": "$15",
  "ExpirationDate": "2027-01-31"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/codes/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "CouponToken": "uuid-v4-string",
        "Value": "$15",
        "ExpirationDate": "2027-01-31"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/update', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    CouponToken: "uuid-v4-string",
    Value: "$15",
    ExpirationDate: "2027-01-31"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/codes/update"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "CouponToken": "uuid-v4-string",
    "Value": "$15",
    "ExpirationDate": "2027-01-31"
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
      "Value": "$15",
      "ExternalURL": "https://shop.example.com/redeem",
      "ExpirationDate": "2027-01-31T00:00:00.000Z",
      "ExpirationTime": "23:59",
      "Status": "not-redeemed",
      "ParticipantToken": null,
      "AssignedTo": null,
      "RedemptionDate": null,
      "RedemptionTime": null,
      "CreationDate": "2026-09-20T14:02:11.004Z"
    }
  },
  "Message": "Code updated successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Code` | Object | The code after the update (same fields as [Fetch Single Code](single.md)); `AssignedTo` is always `null` in this response |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Nothing to update. Send at least one of: CouponCode, Description, Value, ExternalURL, ExpirationDate, ExpirationTime.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CouponToken": "string (required) — UUID v4 of the code",
      "CouponCode": "string (optional) — New code. Only while the code is unassigned and not redeemed; must be unique in the sweepstakes",
      "Description": "string | null (optional) — max 5000 characters",
      "Value": "string | null (optional) — max 10 characters",
      "ExternalURL": "string | null (optional) — https:// link, max 500 characters",
      "ExpirationDate": "string | null (optional) — YYYY-MM-DD",
      "ExpirationTime": "string | null (optional) — HH:mm (24-hour)",
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
  "Message": "The code itself cannot be changed once it is assigned to a participant or redeemed. Other fields can still be updated.",
  "Code": 409,
  "Data": {
    "Code": "CodeInUse"
  }
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This code already exists in the sweepstakes.",
  "Code": 409,
  "Data": {
    "Code": "DuplicateCode"
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
- Sending the same `CouponCode` the code already has is not a rename and is always accepted.
- The rename condition is part of the update itself, so a code assigned or redeemed at the same moment cannot be renamed by a race (`409`).
- Status, assignment and redemption are changed with their own endpoints: [Assign](assign.md), [Unassign](unassign.md), [Redeem](redeem.md), [Unredeem](unredeem.md).
- Writes an entry to the account log at level 3, prefixed `[ API v3 ]`.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
