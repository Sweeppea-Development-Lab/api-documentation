# Fetch Single Calendar Event

Retrieve a specific calendar event by EventToken. The event must belong to the authenticated user.

## Endpoint

`POST /calendar/single`

## Description

This endpoint retrieves a single calendar event by its `EventToken`. The event must belong to the authenticated user, ensuring data privacy and security.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `EventToken` | string | Yes | UUID v4 token of the calendar event |

## Request Example

```json
{
  "EventToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/calendar/single" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"EventToken": "cf1b198f-5c58-4bc6-b944-f466d3e38bc7"}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/calendar/single', {
  method: 'POST',
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

url = "https://api-v3.sweeppea.com/calendar/single"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "EventToken": "cf1b198f-5c58-4bc6-b944-f466d3e38bc7"
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
    "_id": "68df07e9896a685bf51d58cf",
    "EventToken": "uuid-v4-string",
    "SweepstakesToken": "uuid-v4-string",
    "EventLinkedToken": null,
    "EventTitle": "Sweepstakes Launch Event",
    "EventDescription": "Official launch of new sweepstakes campaign",
    "EventLocation": "New York, USA",
    "Latitude": "40.7128",
    "Longitude": "-74.0060",
    "EventURL": "https://example.com/event",
    "EventStartDate": "2026-02-15T00:00:00.000Z",
    "EventStartTime": "09:00",
    "EventEndDate": "2026-02-15T00:00:00.000Z",
    "EventEndTime": "17:00",
    "EventColor": "#6CD9FF",
    "EventAllDay": false,
    "PeopleInvolved": [],
    "EventStatus": true,
    "EventTimeZone": "America/New_York",
    "PrivateEvent": false,
    "RepeatThisEvent": {},
    "Notification": {
      "Frequency": 24,
      "AlertSent": false
    },
    "SMSNotification": false,
    "Automation": "",
    "CreatedByAdmin": false,
    "SerieToken": null,
    "Completed": false,
    "CreationDate": "2026-01-20T10:00:00.000Z"
  }
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
