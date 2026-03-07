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
| `platform` | String | No | Filter by ResourceAffected (e.g., 'renaissance', 'api', 'general', 'overture', 'winners', etc.) |
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
  }
}
```

**401 Unauthorized**

```json
{
  "Response": false,
  "Message": "Invalid or Missing Bearer Token",
  "Code": 401
}
```

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "Invalid API Token",
  "Code": 403
}
```

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal Server Error",
  "Code": 500
}
```

## Notes

- Results are paginated at 20 tickets per page.
- The `Subject` field is truncated to a maximum of 100 characters in the list response.
- Closed tickets have `Status = true` internally.
