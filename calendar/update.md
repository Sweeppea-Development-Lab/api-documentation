# Update Calendar Event

Update a specific calendar event by EventToken. The event must belong to the authenticated user.

## Endpoint

`PUT /calendar/update`

## Description

This endpoint updates a calendar event by its `EventToken`. Only specific fields can be updated. The event must belong to the authenticated user.

**Important:** Events cannot be updated to the past. If updating `EventStartDate` or `EventStartTime`, the new date/time must be in the future.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

### Required Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `EventToken` | string | Yes | UUID v4 token of the calendar event to update |

### Optional Parameters (Updatable Fields)

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `EventTitle` | string | No | Event title |
| `EventDescription` | string | No | Event description |
| `EventLocation` | string | No | Event location |
| `Latitude` | string | No | Location latitude |
| `Longitude` | string | No | Location longitude |
| `EventURL` | string | No | Event URL |
| `EventStartDate` | string | No | Start date in ISO 8601 format |
| `EventStartTime` | string | No | Start time in HH:mm format |
| `EventEndDate` | string | No | End date in ISO 8601 format |
| `EventEndTime` | string | No | End time in HH:mm format |
| `EventColor` | string | No | Event color in hex |
| `EventAllDay` | boolean | No | All day event flag |
| `EventStatus` | boolean | No | Event status (busy/free) |
| `PrivateEvent` | boolean | No | Private event flag |
| `SMSNotification` | boolean | No | SMS notification flag |
| `Completed` | boolean | No | Completion status |

## Request Example

```json
{
  "EventToken": "uuid-v4-string",
  "EventTitle": "Updated Event Title",
  "EventDescription": "Updated description",
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
  "EventStatus": true,
  "PrivateEvent": false,
  "SMSNotification": false,
  "Completed": false
}
```

## Code Examples

### cURL

```bash
curl -X PUT "https://api-v3.sweeppea.com/calendar/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "EventToken": "cf1b198f-5c58-4bc6-b944-f466d3e38bc7",
    "EventTitle": "Updated Event Title",
    "EventColor": "#FF5733"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/calendar/update', {
  method: 'PUT',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    EventToken: 'cf1b198f-5c58-4bc6-b944-f466d3e38bc7',
    EventTitle: 'Updated Event Title',
    EventColor: '#FF5733'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/calendar/update"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "EventToken": "cf1b198f-5c58-4bc6-b944-f466d3e38bc7",
    "EventTitle": "Updated Event Title",
    "EventColor": "#FF5733"
}

response = requests.put(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "Calendar Event Updated Successfully",
  "Data": {
    "_id": "68df07e9896a685bf51d58cf",
    "EventToken": "uuid-v4-string",
    "EventTitle": "Updated Event Title",
    "EventDescription": "Updated description",
    "EventColor": "#FF5733"
  }
}
```

**400 Bad Request** — Missing EventToken

```json
{
  "Response": false,
  "Message": "EventToken is Required",
  "Help": {
    "ExpectedBody": {
      "EventToken": "string (required) \u2014 UUID v4 of the calendar event",
      "EventTitle": "string (optional) \u2014 new title",
      "EventStartDate": "string (optional) \u2014 new start date, YYYY-MM-DD",
      "EventEndDate": "string (optional) \u2014 new end date, YYYY-MM-DD. Cannot be moved into the past",
      "EventStartTime": "string (optional) \u2014 new start time, HH:MM (24-hour)",
      "EventEndTime": "string (optional) \u2014 new end time, HH:MM (24-hour)",
      "EventAllDay": "boolean (optional) \u2014 all-day event",
      "EventDescription": "string (optional) \u2014 new description",
      "EventLocation": "string (optional) \u2014 new location",
      "EventURL": "string (optional) \u2014 new related URL",
      "EventColor": "string (optional) \u2014 new colour",
      "EventStatus": "string (optional) \u2014 new status",
      "Completed": "boolean (optional) \u2014 mark the event as completed",
      "PrivateEvent": "boolean (optional) \u2014 mark the event as private",
      "SMSNotification": "boolean (optional) \u2014 send an SMS notification",
      "Latitude": "number (optional) \u2014 latitude of the location",
      "Longitude": "number (optional) \u2014 longitude of the location"
    }
  },
  "Code": 400
}
```

**400 Bad Request** — Past date

```json
{
  "Response": false,
  "Message": "Cannot Update Events To The Past",
  "Help": {
    "ExpectedBody": {
      "EventToken": "string (required) \u2014 UUID v4 of the calendar event",
      "EventTitle": "string (optional) \u2014 new title",
      "EventStartDate": "string (optional) \u2014 new start date, YYYY-MM-DD",
      "EventEndDate": "string (optional) \u2014 new end date, YYYY-MM-DD. Cannot be moved into the past",
      "EventStartTime": "string (optional) \u2014 new start time, HH:MM (24-hour)",
      "EventEndTime": "string (optional) \u2014 new end time, HH:MM (24-hour)",
      "EventAllDay": "boolean (optional) \u2014 all-day event",
      "EventDescription": "string (optional) \u2014 new description",
      "EventLocation": "string (optional) \u2014 new location",
      "EventURL": "string (optional) \u2014 new related URL",
      "EventColor": "string (optional) \u2014 new colour",
      "EventStatus": "string (optional) \u2014 new status",
      "Completed": "boolean (optional) \u2014 mark the event as completed",
      "PrivateEvent": "boolean (optional) \u2014 mark the event as private",
      "SMSNotification": "boolean (optional) \u2014 send an SMS notification",
      "Latitude": "number (optional) \u2014 latitude of the location",
      "Longitude": "number (optional) \u2014 longitude of the location"
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
  "Message": "Calendar Event Not Found",
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

## Notes

- Only the fields listed in the optional parameters table can be updated.
- If `EventStartDate` or `EventStartTime` is included in the update, the resulting date/time must be in the future.
- The event must belong to the authenticated user or a 404 will be returned.
