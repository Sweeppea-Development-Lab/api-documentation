# Create Support Ticket

Create a new support ticket for the authenticated user. Tickets are automatically assigned to all admin users for review.

## Endpoint

`POST /tickets/create`

## Description

This endpoint creates a new support ticket for the authenticated user. The ticket is automatically assigned to all admin users (UserType = 3) and includes an initial note indicating it was created via API v3. Each ticket receives a unique 7-digit case number for tracking.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `Title` | String | Yes | Ticket subject/title (max 200 characters) |
| `Description` | String | Yes | Detailed description of the issue (max 20,000 characters) |
| `Priority` | Number | Yes | Priority level: 1 (Low), 2 (Medium), 3 (High) |

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tickets/create" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Title": "Issue with sweepstakes entry",
    "Description": "Users are unable to submit entries on the mobile version of the entry page",
    "Priority": 2
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tickets/create', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    Title: 'Issue with sweepstakes entry',
    Description: 'Users are unable to submit entries on the mobile version of the entry page',
    Priority: 2
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/tickets/create"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "Title": "Issue with sweepstakes entry",
    "Description": "Users are unable to submit entries on the mobile version of the entry page",
    "Priority": 2
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**201 Created**

```json
{
  "Response": true,
  "Message": "Ticket Created Successfully",
  "Data": {
    "CaseNumber": "2650478",
    "Subject": "Issue with sweepstakes entry",
    "Priority": 2,
    "ResourceAffected": "General",
    "CreationDate": "2026-01-26T12:32:29.600Z",
    "Status": "Open"
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing Required Fields: Title, Description and Priority are required",
  "Code": 400
}
```

```json
{
  "Response": false,
  "Message": "Invalid Priority Value. Must be 1 (Low), 2 (Medium), or 3 (High)",
  "Code": 400
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
