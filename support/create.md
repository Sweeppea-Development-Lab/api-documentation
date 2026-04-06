# Create Support Ticket

Create a new support ticket. Optionally assign it to a specific admin by name or email; otherwise it is assigned to all admin users.

## Endpoint

`POST /tickets/create`

## Description

Creates a new support ticket for the authenticated user. Each ticket receives a unique 7-digit case number and an initial system note indicating it was created via API v3.

**Assignment behaviour:**

- **Without `AssignTo`** — the ticket is assigned to all admin users (default behaviour).
- **With `AssignTo`** — the system searches for an admin user whose email matches exactly *or* whose full name matches case-insensitively. If a match is found, the ticket is assigned exclusively to that admin. If no match is found, a 400 error is returned.

> **Note:** Only users with admin-level access can be assigned to tickets. Regular users are not eligible.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `Title` | String | Yes | Ticket subject/title (max 200 characters) |
| `Description` | String | Yes | Detailed description of the issue (max 20,000 characters) |
| `Priority` | Number | Yes | Priority level: `1` (Low), `2` (Medium), `3` (High) |
| `AssignTo` | String | No | Name or email of the admin user to assign this ticket to exclusively. If omitted, the ticket is assigned to all admins. Matching is case-insensitive for names and exact for email addresses. |

## Code Examples

### cURL — Assign to all admins (default)

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

### cURL — Assign to a specific admin

```bash
curl -X POST "https://api-v3.sweeppea.com/tickets/create" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Title": "Issue with sweepstakes entry",
    "Description": "Users are unable to submit entries on the mobile version of the entry page",
    "Priority": 2,
    "AssignTo": "support@yourdomain.com"
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
    Priority: 2,
    AssignTo: 'support@yourdomain.com' // optional
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
    "Priority": 2,
    "AssignTo": "support@yourdomain.com"  # optional
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**201 Created**

When `AssignTo` is omitted, `AssignedTo` is `null` (assigned to all admins). When a specific admin is matched, `AssignedTo` contains their name and email.

```json
{
  "Response": true,
  "Message": "Ticket Created Successfully",
  "Data": {
    "CaseNumber": "2650478",
    "Subject": "Issue with sweepstakes entry",
    "Priority": 2,
    "ResourceAffected": "General",
    "AssignedTo": {
      "FullName": "Admin User",
      "Email": "support@yourdomain.com"
    },
    "CreationDate": "2026-01-26T12:32:29.600Z",
    "Status": "Open"
  },
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 142,
    "MaxAPICalls": 100000
  }
}
```

**400 Bad Request — Missing Fields**

```json
{
  "Response": false,
  "Message": "Missing Required Fields: Title, Description and Priority are required",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "Title": "string (required) — Ticket subject/title, max 200 characters",
      "Description": "string (required) — Detailed description of the issue, max 20,000 characters",
      "Priority": "number (required) — Priority level: 1 (Low), 2 (Medium), 3 (High)",
      "AssignTo": "string (optional) — Name or email of an admin user to assign the ticket to exclusively. If omitted, the ticket is assigned to all admins. Only users with admin-level access can be assigned."
    }
  }
}
```

**400 Bad Request — Invalid Priority**

```json
{
  "Response": false,
  "Message": "Invalid Priority Value. Must be 1 (Low), 2 (Medium), or 3 (High)",
  "Code": 400
}
```

**400 Bad Request — Admin Not Found**

```json
{
  "Response": false,
  "Message": "No admin user found matching \"support@yourdomain.com\". Only admin users can be assigned to tickets. Provide a valid admin name or email.",
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
