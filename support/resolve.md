# Resolve Ticket

Close an open support ticket for the authenticated user.

## Endpoint

`POST /tickets/resolve`

## Description

This endpoint allows users to close their own open support tickets. The ticket must belong to the authenticated user and must be in open status (Status = false). When closed, the ticket's status is set to true, completion is set to 100%, and a closing note and resolve statistic are automatically added.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CaseId` | String | Yes | The case number of the ticket to close (e.g., "ABC1234") |

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tickets/resolve" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "CaseId": "ABC1234"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tickets/resolve', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    CaseId: "ABC1234"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/tickets/resolve"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "CaseId": "ABC1234"
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "Ticket Resolved Successfully",
  "Data": {
    "CaseNumber": "ABC1234",
    "Subject": "Payment processing issue",
    "Status": true,
    "CloseDate": "2026-01-26T12:05:35.534Z",
    "ClosedBy": "John Doe"
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing Required Field: CaseId is required",
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
  "Message": "Ticket Not Found or Already Closed",
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

## Notes

- Only tickets with open status (Status = false) can be resolved.
- Upon resolution, the ticket's completion is automatically set to 100%.
- A closing note and a resolve statistic entry are automatically added to the ticket.
