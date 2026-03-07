# Fetch Documentation

Retrieve published documentation articles with pagination and search capabilities.

## Endpoint

`POST /documentation/fetch`

## Description

This endpoint retrieves published documentation articles with support for pagination and full-text search. Results are returned in pages of 5 documents by default, including the full content (Description field). You can search across document titles and content to find specific information.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `page` | Number | No | 1 | Page number to retrieve |
| `limit` | Number | No | 5 | Number of documents per page (max: 10) |
| `search` | String | No | null | Search term to filter documents by title or content |

## Request Example

```json
{
  "page": 1,
  "limit": 5,
  "search": "API"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://apidocs.sweeppea.com/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "page": 1,
    "limit": 5,
    "search": "API"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://apidocs.sweeppea.com/fetch', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    page: 1,
    limit: 5,
    search: 'API'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://apidocs.sweeppea.com/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "page": 1,
    "limit": 5,
    "search": "API"
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "TotalDocuments": 245,
    "CurrentPage": 1,
    "TotalPages": 49,
    "Limit": 5,
    "HasNextPage": true,
    "HasPrevPage": false,
    "Documents": [
      {
        "_id": "68df07e9896a685bf51d58cf",
        "DocumentToken": "uuid-v4-string",
        "DocumentTitle": "Getting Started with Sweeppea API",
        "Description": "Full documentation content here...",
        "Language": "EN",
        "Module": "API",
        "Keywords": "api, getting started, authentication",
        "ResourceAffected": "api",
        "Files": [],
        "Views": 150,
        "Score": [],
        "Status": true,
        "CreationDate": "2025-01-15T10:30:00.000Z",
        "LastUpdate": "2025-01-20T14:45:00.000Z"
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

**404 Not Found**

```json
{
  "Response": false,
  "Message": "User Not Found",
  "Code": 404
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

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `TotalDocuments` | Number | Total number of documentation articles matching the query |
| `CurrentPage` | Number | Current page number |
| `TotalPages` | Number | Total number of pages available |
| `Limit` | Number | Number of documents per page |
| `HasNextPage` | Boolean | Whether there is a next page available |
| `HasPrevPage` | Boolean | Whether there is a previous page available |
| `DocumentToken` | String | Unique identifier for the documentation article (UUID v4) |
| `DocumentTitle` | String | Title of the documentation article |
| `Description` | String | Full content of the documentation article |
| `Language` | String | Language code (e.g., "EN", "ES") |
| `Module` | String | Module or category the documentation belongs to |
| `Keywords` | String | Comma-separated keywords for search optimization |
| `ResourceAffected` | String | Resource type (renaissance, overture, api, etc.) |
| `Views` | Number | Number of times the documentation has been viewed |

## Usage Examples

**Fetch First Page**

```json
{
  "page": 1,
  "limit": 5
}
```

**Search for Specific Topic**

```json
{
  "search": "authentication",
  "page": 1,
  "limit": 5
}
```

**Get More Results Per Page**

```json
{
  "page": 1,
  "limit": 10
}
```

## Notes

- **Pagination:** Results are returned in pages of 5 documents by default. Use `page` and `limit` parameters to navigate through results. Maximum limit is 10 documents per page.
- **Full Content:** The `Description` field containing the full documentation content is included in all responses.
- **Search:** Use the `search` parameter to filter documents by title or content. Search is case-insensitive and supports partial matches.
- **Sorting:** Documents are sorted by creation date in descending order (newest first).
- **Navigation:** Use `HasNextPage` and `HasPrevPage` to determine if more pages are available.
