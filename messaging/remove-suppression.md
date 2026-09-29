# Remove Messaging Suppression

Remove one address that was added to the do-not-contact list by hand (reason manual).

## Endpoint

`POST /messaging/remove-suppression`

## Description

This endpoint removes a single suppression of reason `manual` — an address you (or an integration) added. Identify it by the address itself or by the `AddressHash` returned by [Fetch Suppressions](suppressions.md).

A recipient's own opt-out (`unsubscribe`, `stop`, `complaint`, `bounce`) is never removable, and rows of reason `import` — usually the opt-out list of a previous provider — can only be removed from the Sweeppea app. Both answer `409` with the reason.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Send Message module must also be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `Channel` | String | Yes | `email` or `sms` |
| `Address` | String | Conditional | The email address or phone number. Required unless `AddressHash` is sent |
| `AddressHash` | String | No | The 64-character `AddressHash` from [Fetch Suppressions](suppressions.md). When present, `Address` is ignored |

## Request Example

```json
{
  "Channel": "email",
  "Address": "jane.doe@example.com"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/remove-suppression" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "Channel": "email",
        "Address": "jane.doe@example.com"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/remove-suppression', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    Channel: "email",
    Address: "jane.doe@example.com"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/messaging/remove-suppression"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "Channel": "email",
    "Address": "jane.doe@example.com"
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
    "Channel": "email",
    "Address": "jane.doe@example.com",
    "Reason": "manual"
  },
  "Message": "Address removed from the suppression list"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Channel` | String | The channel |
| `Address` | String | The address that was removed |
| `Reason` | String | The reason of the removed row (always `manual`) |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: Address (or AddressHash)",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "Channel": "string (required) — email | sms",
      "Address": "string (required unless AddressHash is sent) — The email address or phone number",
      "AddressHash": "string (optional) — The 64-character AddressHash returned by /messaging/suppressions, instead of Address",
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
  "Message": "Invalid Address. It is not a valid email address.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "Channel": "string (required) — email | sms",
      "Address": "string (required unless AddressHash is sent) — The email address or phone number",
      "AddressHash": "string (optional) — The 64-character AddressHash returned by /messaging/suppressions, instead of Address",
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
  "Message": "This address is not on your suppression list.",
  "Code": 404
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This address cannot be removed through the API (reason \"unsubscribe\"). Only addresses added by hand (reason manual) can be removed through the API; imported rows are removed from the Sweeppea app.",
  "Code": 409,
  "Data": {
    "Reason": "unsubscribe"
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
  "Message": "The Send Message module is not enabled for your account. Contact support to enable it.",
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

- Requires the Send Message module on the account (`403` otherwise).
- The reason check is part of the delete itself, so an opt-out can never be removed by a race.
- Writes an entry to the account log at level 2, prefixed `[ API v3 ]`, naming the removed address.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
