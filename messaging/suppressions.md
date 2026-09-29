# Fetch Messaging Suppressions

The account's do-not-contact list: every address that unsubscribed, replied STOP, complained, bounced or was added by you.

## Endpoint

`POST /messaging/suppressions`

## Description

This endpoint lists the suppressed addresses of the account, newest first. No email or SMS is ever sent to a suppressed address on that channel — not by a campaign, not by a direct message. Filter by channel or look up one exact address.

`Removable` says which rows [Remove Suppression](remove-suppression.md) may delete: only those added by hand (reason `manual`). A recipient's own opt-out is never the account's to undo.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. It is read-only and does not require the Send Message module.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `Channel` | String | No | `email` or `sms` |
| `Search` | String | No | One exact email address or phone number (max 254 characters). Without `Channel`, a value containing `@` is searched as email, anything else as SMS |
| `Page` | Number | No | Page number, starting at 1. Defaults to `1` |
| `ItemsPerPage` | Number | No | `1` to `100`. Defaults to `25` |

## Request Example

```json
{
  "Channel": "email",
  "Page": 1,
  "ItemsPerPage": 25
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/suppressions" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "Channel": "email",
        "Page": 1,
        "ItemsPerPage": 25
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/suppressions', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    Channel: "email",
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

url = "https://api-v3.sweeppea.com/messaging/suppressions"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "Channel": "email",
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
    "Suppressions": [
      {
        "Channel": "email",
        "Address": "jane.doe@example.com",
        "AddressHash": "3f1c9a0b5e7d2c4f8a6b1e0d9c7f5a3b2e4d6c8a0f1b3d5e7c9a2b4d6f8e0c1a",
        "Reason": "manual",
        "CreationDate": "2026-09-27T12:40:03.221Z",
        "Removable": true
      },
      {
        "Channel": "email",
        "Address": "john.smith@example.com",
        "AddressHash": "9e8d7c6b5a4f3e2d1c0b9a8f7e6d5c4b3a2f1e0d9c8b7a6f5e4d3c2b1a0f9e8d",
        "Reason": "unsubscribe",
        "CreationDate": "2026-09-21T08:15:44.012Z",
        "Removable": false
      }
    ],
    "TotalResults": 2,
    "Page": 1,
    "ItemsPerPage": 25,
    "TotalPages": 1
  },
  "Message": "Suppressions fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Suppressions[].Channel` | String | `email` or `sms` |
| `Suppressions[].Address` | String | The suppressed address (lowercased email, or E.164 phone) |
| `Suppressions[].AddressHash` | String | 64-character SHA-256 hash of the normalized address — accepted by [Remove Suppression](remove-suppression.md) |
| `Suppressions[].Reason` | String | `unsubscribe`, `stop`, `complaint`, `bounce`, `manual` or `import` |
| `Suppressions[].CreationDate` | Date | When the address was suppressed |
| `Suppressions[].Removable` | Boolean | `true` only for reason `manual` |
| `TotalResults / Page / ItemsPerPage / TotalPages` | Number | Pagination |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid Channel. Accepted values: email, sms.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "Channel": "string (optional) — email | sms",
      "Search": "string (optional) — One exact email address or phone number",
      "Page": "number (optional) — Page number, starting at 1. Defaults to 1",
      "ItemsPerPage": "number (optional) — 1 to 100. Defaults to 25",
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

- Read-only: it does not require the Send Message module.
- The search compares hashes of the normalized address, so it is exact — an unparseable value matches nothing.
- Sweeppea also keeps a platform-wide suppression list (addresses no Sweeppea account may contact); it is not listed here but is always honoured.
- Every `200` response carries `Telemetry` with your API calls this month (`APICalls`) and the plan maximum (`MaxAPICalls`).
- Campaigns are created, reviewed and launched only in the Sweeppea app, where launch checks and content moderation run. This API reads them and can pause, resume or cancel them.
