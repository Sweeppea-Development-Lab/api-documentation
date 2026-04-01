# Create Calendar Event

Create a new calendar event for the authenticated user with customizable properties.

## Endpoint

`POST /calendar/create`

## Description

This endpoint creates a new calendar event for the authenticated user. The event is automatically assigned to the user via their API token. `EventToken` and `UserToken` are generated automatically.

**Important:** Events cannot be created in the past. The `EventStartDate` and `EventStartTime` must be in the future.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

### Required Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `EventTitle` | string | Yes | Title of the event |
| `EventStartDate` | string | Yes | Start date in ISO 8601 format |
| `EventEndDate` | string | Yes | End date in ISO 8601 format |

### Optional Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `EventDescription` | string | No | null | Event description |
| `EventLocation` | string | No | null | Event location |
| `Latitude` | string | No | null | Location latitude |
| `Longitude` | string | No | null | Location longitude |
| `EventURL` | string | No | null | Event URL |
| `EventStartTime` | string | No | `00:00` | Start time in HH:mm format |
| `EventEndTime` | string | No | `23:59` | End time in HH:mm format |
| `EventColor` | string | No | `#6CD9FF` | Event color in hex |
| `EventAllDay` | boolean | No | `false` | All day event flag |
| `EventStatus` | boolean | No | `false` | Event status (busy/free) |
| `PrivateEvent` | boolean | — | `true` (hardcoded) | Always `true` for API v3 events — admins cannot see them. Use the calendar UI to make an event public. |
| `SMSNotification` | boolean | No | `false` | SMS notification flag |
| `Completed` | boolean | No | `false` | Completion status |
| `SweepstakesToken` | string | No | `""` | Associated sweepstakes token |
| `PeopleInvolved` | array | No | `[]` | List of people involved |
| `RepeatThisEvent` | object | No | `{}` | Repeat configuration |
| `Notification` | object | No | `{}` | Notification settings |

## Request Example

```json
{
  "EventTitle": "Team Meeting",
  "EventDescription": "Monthly team sync meeting",
  "EventLocation": "Conference Room A",
  "EventStartDate": "2026-02-20T10:00:00.000Z",
  "EventEndDate": "2026-02-20T12:00:00.000Z",
  "EventStartTime": "10:00",
  "EventEndTime": "12:00",
  "EventColor": "#FF5733",
  "EventAllDay": false,
  "EventStatus": false,
  "PrivateEvent": true,
  "SMSNotification": false,
  "Completed": false
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/calendar/create" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "EventTitle": "Team Meeting",
    "EventStartDate": "2026-02-20T10:00:00.000Z",
    "EventEndDate": "2026-02-20T12:00:00.000Z",
    "EventColor": "#FF5733"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/calendar/create', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    EventTitle: 'Team Meeting',
    EventStartDate: '2026-02-20T10:00:00.000Z',
    EventEndDate: '2026-02-20T12:00:00.000Z',
    EventColor: '#FF5733'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/calendar/create"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "EventTitle": "Team Meeting",
    "EventStartDate": "2026-02-20T10:00:00.000Z",
    "EventEndDate": "2026-02-20T12:00:00.000Z",
    "EventColor": "#FF5733"
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**201 Created**

```json
{
  "Response": true,
  "Message": "Calendar Event Created Successfully",
  "Data": {
    "_id": "696f74c08c20c5725ca2aa32",
    "EventToken": "bbc95fa1-0301-498e-bf97-e60fd286d9bc",
    "EventTitle": "Team Meeting",
    "EventDescription": "Monthly team sync meeting",
    "EventStartDate": "2026-02-20T10:00:00.000Z",
    "EventEndDate": "2026-02-20T12:00:00.000Z",
    "EventTimeZone": "1"
  }
}
```

**400 Bad Request** — Missing required field

```json
{
  "Response": false,
  "Message": "EventTitle is Required",
  "Code": 400
}
```

**400 Bad Request** — Past date

```json
{
  "Response": false,
  "Message": "Cannot Create Events In The Past",
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

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal Server Error",
  "Code": 500
}
```

## Notes

- `EventToken` and `UserToken` are generated automatically by the server.
- Dates must be provided in ISO 8601 format (e.g., `2026-02-20T10:00:00.000Z`).
- Times must be provided in HH:mm format (e.g., `10:00`).
- Events cannot be scheduled in the past — both `EventStartDate` and `EventStartTime` must refer to a future date/time.
