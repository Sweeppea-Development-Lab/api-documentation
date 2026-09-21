# Delete Support Ticket

Delete an open support ticket created by the authenticated user.

## Endpoint

`POST /tickets/delete`

## Description

This endpoint permanently deletes an open support ticket. Only tickets that are open (Status = false) and created by the authenticated user can be deleted.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `CaseId` | String | Yes | 7-digit case number of the ticket to delete |

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tickets/delete" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "CaseId": "2531581"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tickets/delete', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    CaseId: '2531581'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/tickets/delete"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "CaseId": "2531581"
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "Ticket Deleted Successfully"
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing Required Field: CaseId is required",
  "Help": {
    "ExpectedBody": {
      "CaseId": "string (required) \u2014 case id of the ticket"
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
  "Message": "Ticket Not Found, Already Closed, or Not Owned by User",
  "Code": 404
}
```

**409 Conflict**

The ticket is locked while our engineering team works on it (an engineering issue is still open or a fix is still in progress). It can be deleted once that work is finished.

```json
{
  "Response": false,
  "Message": "Ticket ABC1234 is locked while our engineering team works on it. It can be deleted once that work is finished.",
  "Code": 409
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

- Only open tickets (Status = false) can be deleted. Closed tickets cannot be deleted.
- The ticket must have been created by the authenticated user. Tickets owned by other users will return a 404.
- This operation is permanent and cannot be undone.
