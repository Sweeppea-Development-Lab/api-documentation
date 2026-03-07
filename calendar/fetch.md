# Fetch Calendar Events

Retrieve all calendar events for the authenticated user. Returns past, present, and future events with all properties.

## Endpoint

`GET /calendar/fetch`

## Description

This endpoint retrieves all calendar events associated with the authenticated user. Events are returned sorted by start date in ascending order, including past, present, and future events with all their properties.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Code Examples

### cURL

```bash
curl -X GET "https://api-v3.sweeppea.com/calendar/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/calendar/fetch', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/calendar/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

response = requests.get(url, headers=headers)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "TotalEvents": 2,
    "Events": [
      {
        "_id": "68df07e9896a685bf51d58cf",
        "EventToken": "uuid-v4-string",
        "SweepstakesToken": "uuid-v4-string",
        "CreationDate": "2025-10-02T23:16:57.491Z",
        "Type": "start",
        "EventLinkedToken": null,
        "EventTitle": "Sweepstakes Launch",
        "EventDescription": "Launch of new sweepstakes campaign",
        "EventLocation": "Online",
        "Latitude": null,
        "Longitude": null,
        "EventURL": "https://example.com",
        "EventStartDate": "2025-10-15T00:00:00.000Z",
        "EventStartTime": "09:00",
        "EventEndDate": "2025-10-15T00:00:00.000Z",
        "EventEndTime": "17:00",
        "EventColor": "#6CD9FF",
        "EventAllDay": false,
        "PeopleInvolved": [],
        "EventStatus": true,
        "EventTimeZone": "America/New_York",
        "PrivateEvent": false,
        "RepeatThisEvent": {},
        "Notification": {},
        "SMSNotification": false,
        "Automation": null,
        "CreatedByAdmin": false,
        "SerieToken": null,
        "ChecklistId": null,
        "Completed": false
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

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal Server Error",
  "Code": 500
}
```
