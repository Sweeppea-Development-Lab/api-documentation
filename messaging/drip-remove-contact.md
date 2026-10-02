# Remove Contact From Drip

Takes one contact out of a drip campaign so it receives no more steps.

## Endpoint

`POST /messaging/drip-remove-contact`

## Description

Ends the active enrolment of the contact identified by `SourceToken` with exit reason `removed`. Messages already sent are kept in the report.

Find the `SourceToken` with [List Drip Enrollments](drip-enrollments.md). It does not unsubscribe the contact: to stop all messages to an address use [Add Suppressions](add-suppressions.md).

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Send Message module and the Drip Marketing module must be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `DripToken` | string | Yes | UUID v4 of the drip. |
| `SourceToken` | string | Yes | The contact: a ParticipantToken, or a lead / subscriber id. |
| `CalledFromMCP` | boolean | No | Set by the MCP server, ignore it. |
| `CalledFromCLI` | boolean | No | Set by the CLI, ignore it. |

## Request Example

```json
{
  "DripToken": "uuid-v4-string",
  "SourceToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/drip-remove-contact" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "DripToken": "uuid-v4-string",
        "SourceToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/drip-remove-contact', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    DripToken: "uuid-v4-string",
    SourceToken: "uuid-v4-string"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/messaging/drip-remove-contact"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "DripToken": "uuid-v4-string",
    "SourceToken": "uuid-v4-string"
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
    "DripToken": "uuid-v4-string",
    "SourceToken": "uuid-v4-string",
    "Removed": 1
  },
  "Message": "Contact removed from the drip"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `DripToken` | string | The drip. |
| `SourceToken` | string | The contact. |
| `Removed` | number | Enrolments ended (normally 1). |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: DripToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "DripToken": "string (required) — UUID v4 of the drip",
      "SourceToken": "string (required) — The contact: a ParticipantToken, or the id of a lead / list subscriber (as returned by /messaging/drip-enrollments)",
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
  "Message": "Drip not found. The DripToken must exist and belong to your account.",
  "Code": 404
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "This contact is not enrolled in this drip.",
  "Code": 404
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This contact is not in the sequence any more (status \"completed\").",
  "Code": 409,
  "Data": {
    "Status": "completed",
    "ExitReason": null
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

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "The Drip Marketing module is not enabled for your account. Contact support to enable it.",
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

- A removed contact is not enrolled again by the same drip (one enrolment per address per drip, ever).
- Recorded in your account log.
