# Fetch To-Do's

Retrieve all To-Do's with advanced filtering and pagination. Admin access required.

## Endpoint

`POST /tickets/fetchTodos`

## Description

This endpoint retrieves all To-Do's in the system, paginated at 20 items per page and sorted by creation date (newest first). It is restricted to admin users only.

All filter parameters are optional and can be combined freely. Filters support: text search across Title and Description, status (pending/completed), priority level, resource affected, pinned state, date ranges, and year/month filtering.

## Authentication & Authorization

This endpoint is restricted to administrator accounts. Regular users will receive a `403 Forbidden` response.

## Request Parameters (all optional)

| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | Number | Page number for pagination (default: 1, 20 items per page) |
| `search` | String | Search text in Title or Description (case-insensitive) |
| `status` | String | `"pending"` (open) or `"completed"` |
| `priority` | Number | Filter by priority: `1` = Low, `2` = Medium, `3` = High |
| `resource` | String | Filter by ResourceAffected (e.g. `"renaissance"`, `"api"`, `"aws"`, `"general"`) |
| `pinned` | Boolean | Set to `true` to return only pinned To-Do's |
| `dateFrom` | String | ISO date string — start of creation date range (e.g. `"2025-01-01"`) |
| `dateTo` | String | ISO date string — end of creation date range (e.g. `"2025-12-31"`) |
| `year` | Number | Filter by creation year (ignored if `dateFrom`/`dateTo` are set) |
| `month` | Number | Filter by creation month 1–12 (requires `year`) |

## Request Example

```json
{
  "page": 1,
  "search": "deploy",
  "priority": 3,
  "resource": "api",
  "status": "pending"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tickets/fetchTodos" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "page": 1,
    "search": "deploy",
    "priority": 3,
    "resource": "api",
    "status": "pending"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tickets/fetchTodos', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    page: 1,
    search: 'deploy',
    priority: 3,
    resource: 'api',
    status: 'pending'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/tickets/fetchTodos"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "page": 1,
    "search": "deploy",
    "priority": 3,
    "resource": "api",
    "status": "pending"
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
    "TotalTodos": 42,
    "Page": 1,
    "Limit": 20,
    "TotalPages": 3,
    "Todos": [
      {
        "TodoToken": "uuid-v4-string",
        "CreationDate": "2025-06-01T10:00:00.000Z",
        "Deadline": null,
        "Title": "Deploy new API endpoint",
        "Description": "Deploy the fetchTodos lambda to production",
        "Status": false,
        "Priority": 3,
        "Pin": false,
        "Views": 5,
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
  "Message": "Admin Access Required",
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
