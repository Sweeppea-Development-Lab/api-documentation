# Fetch Single Ticket

Retrieve complete details of a single support ticket by CaseNumber.

## Endpoint

`POST /tickets/fetchSingleTicket`

## Description

This endpoint retrieves complete details of a single support ticket by its CaseNumber. The ticket must belong to the authenticated user. Returns all ticket information including notes, files, collaborators, and statistics.

Sweeppea-internal details are not returned: `Statistics[].UserToken` is only filled for your own actions (actions by Sweeppea Support show `null`) and `GitHubIssue` is always an empty object — `EngineeringInProgress` is `true` while our engineering team works on the ticket (Resolve Ticket answers `409` meanwhile). Automatic closing: when Sweeppea Support resolves a ticket it can keep it open for a few days before it closes by itself. While that is scheduled the ticket is still open and `AutoClose` says when it closes (`ClosesAt`, `Days`); otherwise `AutoClose` is `null`. Adding information to the ticket with Update Ticket cancels the automatic closing. `ClosedAutomatically` is `true` once a ticket closed by itself that way.

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
      },
      {
        "Id": "<stat-id>",
        "Date": "2025-01-27T09:00:00.000Z",
        "UserToken": null,
        "Action": "degrade"
      }
    ],
    "Status": false,
    "AttachmentInfo": {},
    "GitHubIssue": {},
    "EngineeringInProgress": false,
    "AutoClose": {
      "ClosesAt": "2025-02-01T09:00:00.000Z",
      "Days": 5
    },
    "ClosedAutomatically": false
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
  "Message": "Missing caseNumber",
  "Help": {
    "ExpectedBody": {
      "CaseNumber": "string (required) \u2014 case number of the ticket"
    }
  },
  "Code": 400
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
  "Message": "Internal server error. Please try again; if it persists, contact support with the time of this request.",
  "Code": 500
}
```
