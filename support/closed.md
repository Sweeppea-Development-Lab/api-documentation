# Fetch Closed Tickets

Retrieve all closed support tickets for the authenticated user.

## Endpoint

`POST /tickets/fetchClosedTickets`

## Description

This endpoint retrieves all closed support tickets associated with the authenticated user. Tickets are returned in descending order by creation date. Only tickets with Status = true (closed) are included in the results. The Subject field is truncated to 100 characters maximum.

You can filter tickets by search term, platform (ResourceAffected), and priority level.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

All parameters are optional.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `search` | String | No | Search term to filter tickets by Subject or Description (case-insensitive) |
| `platform` | String | No | Filter by platform (ResourceAffected). One of: `general`, `renaissance`, `overture`, `winners`, `soprano`, `symphony`, `sonata`, `papyrus`, `website`, `blog`, `enewsletter`, `socialmedia`, `api`, `aws`, `shopify`, `instakes`, `mcp-server`, `sweeppea-cli`, `n8n`, `other`. Case-insensitive; common spellings (`Shopify App`, `mcp`, `cli`, `Command Line Interface (CLI)`) are aliased. An unrecognized value returns `400`. |
| `priority` | Number | No | Filter by Priority: 1 (Low), 2 (Medium), 3 (High) |
| `page` | Number | No | Page number for pagination (default: 1, 20 tickets per page) |

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tickets/fetchClosedTickets" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "search": "login issue",
    "platform": "renaissance",
    "priority": 3,
    "page": 1
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tickets/fetchClosedTickets', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    search: 'login issue',
    platform: 'renaissance',
    priority: 3,
    page: 1
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/tickets/fetchClosedTickets"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "search": "login issue",
    "platform": "renaissance",
    "priority": 3,
    "page": 1
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "TotalTickets": 45,
    "Page": 1,
    "Limit": 20,
    "TotalPages": 3,
    "Tickets": [
      {
        "CaseNumber": "ABC1234",
        "Subject": "Issue with sweepstakes configuration (truncated to 100 chars max)",
        "Date": "2025-01-26T10:00:00.000Z",
        "Priority": 2,
        "ResourceAffected": "renaissance"
      },
      {
        "CaseNumber": "XYZ5678",
        "Subject": "API integration question",
        "Date": "2025-01-25T14:00:00.000Z",
        "Priority": 1,
        "ResourceAffected": "api"
      }
    ]
  },
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 142,
    "MaxAPICalls": 100000
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid platform value. Must be one of: general, renaissance, overture, winners, soprano, symphony, sonata, papyrus, website, blog, enewsletter, socialmedia, api, aws, shopify, instakes, mcp-server, sweeppea-cli, n8n, other",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "search": "string (optional) — Case-insensitive text matched against Subject or Description",
      "platform": "string (optional) — Filter by platform (ResourceAffected). One of: general, renaissance, overture, winners, soprano, symphony, sonata, papyrus, website, blog, enewsletter, socialmedia, api, aws, shopify, instakes, mcp-server, sweeppea-cli, n8n, other. Case-insensitive; common spellings (Shopify App, mcp, cli, Command Line Interface (CLI)) are aliased.",
      "priority": "number (optional) — Filter by priority: 1 (Low), 2 (Medium), 3 (High)",
      "page": "number (optional) — Page number, 20 tickets per page. Defaults to 1."
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

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal server error. Please try again; if it persists, contact support with the time of this request.",
  "Code": 500
}
```

## Notes

- Results are paginated at 20 tickets per page.
- `Telemetry.APICalls` is the number of API calls this API token made since the start of the current month; `Telemetry.MaxAPICalls` is the monthly limit of the account's plan.
- The `Subject` field is truncated to a maximum of 100 characters in the list response.
- Closed tickets have `Status = true` internally.
