# Assign Code

Assign one available code to one specific participant of the same sweepstakes.

## Endpoint

`POST /codes/assign`

## Description

This endpoint links a code to a participant you name. The code must be unassigned and not redeemed; the participant (a regular or AMOE entry) must belong to the code's sweepstakes and hold no code yet. Both halves are claimed conditionally, and if the participant is claimed by someone else in the meantime the code is released again — a code is never left pointing at somebody who does not point back.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Codes & Coupons module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CouponToken` | String | Yes | UUID v4 of an unassigned, not-redeemed code |
| `ParticipantToken` | String | Yes | UUID v4 of a participant (regular or AMOE) of the same sweepstakes who holds no code |

## Request Example

```json
{
  "CouponToken": "uuid-v4-string",
  "ParticipantToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/codes/assign" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "CouponToken": "uuid-v4-string",
        "ParticipantToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/codes/assign', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    CouponToken: "uuid-v4-string",
    ParticipantToken: "uuid-v4-string"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/codes/assign"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "CouponToken": "uuid-v4-string",
    "ParticipantToken": "uuid-v4-string"
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
      "AssignedTo": null,
      "RedemptionDate": null,
      "RedemptionTime": null,
      "CreationDate": "2026-09-20T14:02:11.004Z"
    }
  },
  "Message": "Code assigned successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Code` | Object | The code after the assignment (same fields as [Fetch Single Code](single.md)); `AssignedTo` is `null` in this response — use Fetch Single Code to read it |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: ParticipantToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "CouponToken": "string (required) — UUID v4 of an unassigned, not-redeemed code",
      "ParticipantToken": "string (required) — UUID v4 of a participant (regular or AMOE) of the same sweepstakes, who holds no code yet",
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

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Participant not found in this code's sweepstakes.",
  "Code": 404
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This code is not available: it is already assigned, redeemed or voided.",
  "Code": 409,
  "Data": {
    "Code": "CodeNotAvailable",
    "Status": "redeemed",
    "Assigned": true
  }
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This participant already holds a code. Unassign it first.",
  "Code": 409,
  "Data": {
    "Code": "ParticipantHasCode",
    "CouponToken": "uuid-v4-string"
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
- Pick an available code with [Fetch Codes](fetch.md) and `Filter: "available"`.
- Assigning does not notify the participant; send the code with [Send Code to Participant](send.md).
- Writes an entry to the account log at level 3, prefixed `[ API v3 ]`.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Purging all the codes of a sweepstakes, removing duplicates and importing codes from a CSV file stay in the Sweeppea app. Every write endpoint of this category requires the Codes & Coupons module; reads do not.
