# Fetch Single Ticket

Retrieve complete details of a single support ticket by CaseNumber.

## Endpoint

`POST /tickets/fetchSingleTicket`

## Description

This endpoint retrieves complete details of a single support ticket by its CaseNumber. The ticket must belong to the authenticated user. Returns all ticket information including notes, files, collaborators, and statistics.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `caseNumber` | String | Yes | The ticket case number (e.g., "HYXTNJV") |

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tickets/fetchSingleTicket" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "caseNumber": "HYXTNJV"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tickets/fetchSingleTicket', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    caseNumber: 'HYXTNJV'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/tickets/fetchSingleTicket"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "caseNumber": "HYXTNJV"
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
    "CaseNumber": "HYXTNJV",
    "Date": "2025-01-26T10:00:00.000Z",
    "AccountInfo": {
      "UserToken": "<user-token>",
      "FullName": "John Doe",
      "Email": "john.doe@example.com",
      "MobileNumber": "5551234567",
      "UserType": "Administrator",
      "UserAgent": "Mozilla/5.0...",
      "IpAddress": "192.168.1.1",
      "Language": "en",
      "GeoLat": 40.7128,
      "GeoLon": -74.0060,
      "SweepstakesName": "Summer Giveaway 2025"
    },
    "Subject": "Issue with sweepstakes configuration",
    "Description": "<p>Unable to configure the entry page settings...</p>",
    "Priority": 2,
    "NotifyUsers": ["support@example.com"],
    "Collaborators": [],
    "ClosedBy": null,
    "CloseDate": null,
    "Notes": [
      {
        "Id": "<note-id>",
        "Date": "2025-01-26T11:00:00.000Z",
        "Note": "Investigating the issue..."
      }
    ],
    "Files": [],
    "Survey": {},
    "Pin": false,
    "Views": 5,
    "Completion": 50,
    "ResourceAffected": "renaissance",
    "Documentation": [],
    "Statistics": [
      {
        "Id": "<stat-id>",
        "Date": "2025-01-26T10:00:00.000Z",
        "UserToken": "<user-token>",
        "Action": "create"
      }
    ],
    "Status": false,
    "AttachmentInfo": {},
    "GitHubIssue": {}
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing caseNumber",
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
  "Message": "Ticket Not Found or Access Denied",
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
