# Delete Calendar Event

Permanently delete a calendar event by EventToken. The event must belong to the authenticated user.

## Endpoint

`DELETE /calendar/delete`

## Description

This endpoint permanently deletes a calendar event by its `EventToken`. The event must belong to the authenticated user. This action cannot be undone.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `EventToken` | string | Yes | UUID v4 token of the calendar event to delete |

## Request Example

```json
{
  "EventToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X DELETE "https://api-v3.sweeppea.com/calendar/delete" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"EventToken": "cf1b198f-5c58-4bc6-b944-f466d3e38bc7"}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/calendar/delete', {
  method: 'DELETE',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    EventToken: 'cf1b198f-5c58-4bc6-b944-f466d3e38bc7'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/calendar/delete"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "EventToken": "cf1b198f-5c58-4bc6-b944-f466d3e38bc7"
}

response = requests.delete(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "Calendar Event Deleted Successfully"
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "EventToken is Required",
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
  "Message": "Calendar Event Not Found",
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

- This action is irreversible. Once deleted, the event cannot be recovered.
- The event must belong to the authenticated user or a 404 will be returned.
