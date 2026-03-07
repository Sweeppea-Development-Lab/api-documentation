# Update Support Ticket

Update an existing support ticket by modifying its title or description. Only open tickets owned by the authenticated user can be updated.

## Endpoint

`POST /tickets/update`

## Description

This endpoint allows updating an existing support ticket. You can modify the title and description. The ticket must be open (not closed) and must belong to the authenticated user. Each update action is tracked in the ticket's statistics.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CaseId` | String | Yes | The 7-digit case number of the ticket to update |
| `Title` | String | No | New ticket title/subject (max 200 characters) |
| `Description` | String | No | New ticket description (max 20,000 characters) |

**Note:** At least one of `Title` or `Description` must be provided.

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tickets/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "CaseId": "2650478",
    "Title": "UPDATED: Issue with sweepstakes entry",
    "Description": "Users are unable to submit entries on both mobile and desktop versions"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tickets/update', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    CaseId: '2650478',
    Title: 'UPDATED: Issue with sweepstakes entry',
    Description: 'Users are unable to submit entries on both mobile and desktop versions'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/tickets/update"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "CaseId": "2650478",
    "Title": "UPDATED: Issue with sweepstakes entry",
    "Description": "Users are unable to submit entries on both mobile and desktop versions"
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "Ticket updated successfully",
  "Ticket": {
    "CaseNumber": "2650478",
    "Subject": "UPDATED: Issue with sweepstakes entry",
    "Description": "Users are unable to submit entries on both mobile and desktop versions",
    "Priority": 2,
    "Status": false
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "CaseId is required"
}
```

```json
{
  "Response": false,
  "Message": "At least one field (Title or Description) must be provided"
}
```

```json
{
  "Response": false,
  "Message": "Cannot update closed ticket"
}
```

**401 Unauthorized**

```json
{
  "Response": false,
  "Message": "Unauthorized: Missing token"
}
```

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "You do not have permission to update this ticket"
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Ticket not found"
}
```

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal server error: [error details]"
}
```
