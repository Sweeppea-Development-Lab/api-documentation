# Schedule Drawing

Schedule a winner drawing for your sweepstakes at a specific date and time, or set up recurring drawings.

## Endpoint

`POST /winners/schedule`

## Description

This endpoint allows you to schedule a winner drawing for your sweepstakes. You can configure the drawing to run at a specific date and time, or set up recurring drawings with various frequency options.

**Important Notes:**

- The sweepstakes must belong to the authenticated user
- At least one eligible participant (without winner status) is required
- The number of winners to pick cannot exceed available participants
- If a group is specified, it must exist for the sweepstakes
- Scheduled drawings can optionally be added to your calendar
- Schedule date and time must be in the present or future (cannot schedule in the past)
- For today's date, the time must not have already passed

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sweepstakesToken` | String (UUID v4) | Required | The unique identifier of the sweepstakes |
| `group` | String | Required | Group token or `"allgroups"` to include all participants |
| `selectedAction` | Number | Required | Action type: `1` = Draw Winners, `2` = Draw Winners & Notify |
| `scheduleMode` | String | Required | `"schedule"` for specific date/time or `"settime"` for period of time |
| `howManyWinnersToPick` | Number | Required | Number of winners to select (must be >= 1) |
| `frequency` | Number | Optional | `0`=None, `1`=Hourly, `2`=Daily, `3`=Weekly, `4`=Monthly |
| `dayOfTheWeek` | Number | Optional | `1`=Sunday, `2`=Monday, ..., `7`=Saturday (for weekly/monthly) |
| `weekOfTheMonth` | Number | Optional | `1`=1st, `2`=2nd, `3`=3rd, `4`=4th week (for monthly frequency) |
| `endDate` | Date | Required | Drawing date (for schedule mode) or end date (for settime mode) |
| `endTime` | String | Required | Drawing time in HH:mm format |
| `deliveryTime` | String | Optional | Delivery time for settime mode |
| `timezone` | Number | Required | Timezone ID (integer) for the scheduled drawing |
| `message` | String | Optional | Message to send to winners (for action 2) |
| `includeOptedOutParticipants` | Boolean | Optional | Include opted-out participants in drawing |
| `publishToWinnersPage` | Boolean | Optional | Publish winners to public winners page |
| `sendCopyToMe` | Boolean | Optional | Send a copy of notification to account owner |
| `doNotIncludeSpamParticipants` | Boolean | Optional | Exclude participants flagged as spam |
| `addDrawingToCalendar` | Boolean | Optional | Add scheduled drawing to calendar |

## Request Example

```json
{
  "sweepstakesToken": "uuid-v4-string",
  "group": "allgroups",
  "selectedAction": 1,
  "scheduleMode": "schedule",
  "howManyWinnersToPick": 1,
  "frequency": 0,
  "endDate": "2025-02-15",
  "endTime": "15:00",
  "timezone": 1,
  "includeOptedOutParticipants": false,
  "publishToWinnersPage": false,
  "sendCopyToMe": false,
  "doNotIncludeSpamParticipants": true,
  "addDrawingToCalendar": true
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/winners/schedule" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "sweepstakesToken": "uuid-v4-string",
    "group": "allgroups",
    "selectedAction": 1,
    "scheduleMode": "schedule",
    "howManyWinnersToPick": 1,
    "frequency": 0,
    "endDate": "2025-02-15",
    "endTime": "15:00",
    "timezone": 1,
    "includeOptedOutParticipants": false,
    "publishToWinnersPage": false,
    "sendCopyToMe": false,
    "doNotIncludeSpamParticipants": true,
    "addDrawingToCalendar": true
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/winners/schedule', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    sweepstakesToken: "uuid-v4-string",
    group: "allgroups",
    selectedAction: 1,
    scheduleMode: "schedule",
    howManyWinnersToPick: 1,
    frequency: 0,
    endDate: "2025-02-15",
    endTime: "15:00",
    timezone: 1,
    includeOptedOutParticipants: false,
    publishToWinnersPage: false,
    sendCopyToMe: false,
    doNotIncludeSpamParticipants: true,
    addDrawingToCalendar: true
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/winners/schedule"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "sweepstakesToken": "uuid-v4-string",
    "group": "allgroups",
    "selectedAction": 1,
    "scheduleMode": "schedule",
    "howManyWinnersToPick": 1,
    "frequency": 0,
    "endDate": "2025-02-15",
    "endTime": "15:00",
    "timezone": 1,
    "includeOptedOutParticipants": False,
    "publishToWinnersPage": False,
    "sendCopyToMe": False,
    "doNotIncludeSpamParticipants": True,
    "addDrawingToCalendar": True
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Message": "Drawing scheduled successfully."
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
  "Message": "Invalid API token, call support.",
  "Code": 403
}
```

**400 Bad Request - Invalid Parameters**
```json
{
  "Response": false,
  "Message": "Invalid parameters in body object, read documentation.",
  "Code": 400
}
```

**404 Not Found - Sweepstakes**
```json
{
  "Response": false,
  "Message": "Sweepstakes not found.",
  "Code": 404
}
```

**403 Forbidden - No Permission**
```json
{
  "Response": false,
  "Message": "You do not have permission to access this sweepstakes.",
  "Code": 403
}
```

**404 Not Found - Group**
```json
{
  "Response": false,
  "Message": "Group not found for this sweepstakes.",
  "Code": 404
}
```

**400 Bad Request - No Eligible Participants**
```json
{
  "Response": false,
  "Message": "No eligible participants found for this sweepstakes. At least one participant without winner status is required.",
  "Code": 400
}
```

**400 Bad Request - Invalid Winners Count**
```json
{
  "Response": false,
  "Message": "Invalid howManyWinnersToPick parameter. Must be at least 1.",
  "Code": 400
}
```

**400 Bad Request - Not Enough Participants**
```json
{
  "Response": false,
  "Message": "Not enough eligible participants. Requested: 5, Available: 3",
  "Code": 400
}
```

**400 Bad Request - Invalid Schedule Date**
```json
{
  "Response": false,
  "Message": "Invalid schedule date/time. The scheduled drawing must be in the present or future.",
  "Code": 400
}
```
