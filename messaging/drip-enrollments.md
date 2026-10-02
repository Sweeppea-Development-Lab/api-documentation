# List Drip Enrollments

Lists the contacts enrolled in a drip campaign: where each one is in the sequence and why anyone left.

## Endpoint

`POST /messaging/drip-enrollments`

## Description

Returns one page of enrolments, newest first. Filter by `Status` or look up one contact with `SourceToken`.

A contact is identified by its source: a `ParticipantToken`, or the id of a lead or list subscriber. Email addresses and phone numbers are never returned.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. The Drip Marketing module must be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `DripToken` | string | Yes | UUID v4 of the drip. |
| `Status` | string | No | `active`, `completed` or `exited`. |
| `SourceToken` | string | No | Only this contact (a ParticipantToken, or a lead / subscriber id). |
| `Page` | number | No | Page number, starting at 1. Default 1. |
| `ItemsPerPage` | number | No | 1 to 100. Default 25. |
| `CalledFromMCP` | boolean | No | Set by the MCP server, ignore it. |
| `CalledFromCLI` | boolean | No | Set by the CLI, ignore it. |

## Request Example

```json
{
  "DripToken": "uuid-v4-string",
  "Status": "active",
  "Page": 1
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/drip-enrollments" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "DripToken": "uuid-v4-string",
        "Status": "active",
        "Page": 1
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/drip-enrollments', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    DripToken: "uuid-v4-string",
    Status: "active",
    Page: 1
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/messaging/drip-enrollments"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "DripToken": "uuid-v4-string",
    "Status": "active",
    "Page": 1
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
    "Enrollments": [
      {
        "EnrollmentToken": "uuid-v4-string",
        "SourceType": "participant",
        "SourceToken": "uuid-v4-string",
        "Status": "active",
        "ExitReason": null,
        "NextStep": 2,
        "NextStepAt": "2026-10-04T15:00:00.000Z",
        "EnrolledAt": "2026-10-01T14:58:12.000Z",
        "LastSentAt": "2026-10-01T16:00:03.000Z",
        "EndedAt": null
      }
    ],
    "TotalResults": 1,
    "Page": 1,
    "ItemsPerPage": 25,
    "TotalPages": 1
  },
  "Message": "Drip enrollments fetched successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Enrollments[].SourceType` | string | `participant`, `amoe`, `lead` or `subscriber`. |
| `Enrollments[].SourceToken` | string | The contact id — use it with [Remove Contact From Drip](drip-remove-contact.md). |
| `Enrollments[].Status` | string | `active`, `completed` or `exited`. |
| `Enrollments[].ExitReason` | string|null | `unsubscribed`, `suppressed`, `no_consent`, `removed`, `clicked`, `won`, `invalid`, `undeliverable` or `over_quota`. |
| `Enrollments[].NextStep / NextStepAt` | number|null | 1-based step the contact waits for and when it is due (active only). |
| `Enrollments[].EnrolledAt / LastSentAt / EndedAt` | string|null | ISO 8601 dates. |
| `TotalResults / Page / ItemsPerPage / TotalPages` | number | Pagination. |

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
      "Status": "string (optional) — active | completed | exited",
      "SourceToken": "string (optional) — Only the enrolment(s) of this contact (a ParticipantToken, or the id of a lead / list subscriber)",
      "Page": "number (optional) — Page number, starting at 1. Defaults to 1",
      "ItemsPerPage": "number (optional) — 1 to 100. Defaults to 25",
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

- Ended enrolments are kept 180 days.
- Read-only.
