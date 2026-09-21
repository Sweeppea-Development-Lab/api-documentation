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

> **Parameter Casing:** All request parameters are `PascalCase`. For backward compatibility, `camelCase` equivalents (e.g. `sweepstakesToken`) are also accepted.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String (UUID v4) | Required | The unique identifier of the sweepstakes |
| `Group` | String | Required | Group token or `"allgroups"` to include all participants |
| `SelectedAction` | Number | Required | Action type: `1` = Draw Winners, `2` = Draw Winners & Notify |
| `ScheduleMode` | String | Required | `"schedule"` for specific date/time or `"settime"` for period of time |
| `HowManyWinnersToPick` | Number | Required | Number of winners to select (must be >= 1) |
| `Frequency` | Number | Optional | `0`=None, `1`=Hourly, `2`=Daily, `3`=Weekly, `4`=Monthly |
| `DayOfTheWeek` | Number | Optional | `1`=Sunday, `2`=Monday, ..., `7`=Saturday (for weekly/monthly) |
| `WeekOfTheMonth` | Number | Optional | `1`=1st, `2`=2nd, `3`=3rd, `4`=4th week (for monthly frequency) |
| `EndDate` | Date | Required | Drawing date (for schedule mode) or end date (for settime mode) |
| `EndTime` | String | Required | Drawing time in HH:mm format |
| `DeliveryTime` | String | Optional | Delivery time for settime mode |
| `Timezone` | Number | Required | Timezone ID (integer) for the scheduled drawing |
| `Message` | String | Optional | Message to send to winners (for action 2) |
| `IncludeOptedOutParticipants` | Boolean | Optional | Include opted-out participants in drawing |
| `PublishToWinnersPage` | Boolean | Optional | Publish winners to public winners page |
| `SendCopyToMe` | Boolean | Optional | Send a copy of notification to account owner |
| `DoNotIncludeSpamParticipants` | Boolean | Optional | Exclude participants flagged as spam |
| `AddDrawingToCalendar` | Boolean | Optional | Add scheduled drawing to calendar |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "Group": "allgroups",
  "SelectedAction": 1,
  "ScheduleMode": "schedule",
  "HowManyWinnersToPick": 1,
  "Frequency": 0,
  "EndDate": "2025-02-15",
  "EndTime": "15:00",
  "Timezone": 1,
  "IncludeOptedOutParticipants": false,
  "PublishToWinnersPage": false,
  "SendCopyToMe": false,
  "DoNotIncludeSpamParticipants": true,
  "AddDrawingToCalendar": true
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/winners/schedule" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "uuid-v4-string",
    "Group": "allgroups",
    "SelectedAction": 1,
    "ScheduleMode": "schedule",
    "HowManyWinnersToPick": 1,
    "Frequency": 0,
    "EndDate": "2025-02-15",
    "EndTime": "15:00",
    "Timezone": 1,
    "IncludeOptedOutParticipants": false,
    "PublishToWinnersPage": false,
    "SendCopyToMe": false,
    "DoNotIncludeSpamParticipants": true,
    "AddDrawingToCalendar": true
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
    SweepstakesToken: "uuid-v4-string",
    Group: "allgroups",
    SelectedAction: 1,
    ScheduleMode: "schedule",
    HowManyWinnersToPick: 1,
    Frequency: 0,
    EndDate: "2025-02-15",
    EndTime: "15:00",
    Timezone: 1,
    IncludeOptedOutParticipants: false,
    PublishToWinnersPage: false,
    SendCopyToMe: false,
    DoNotIncludeSpamParticipants: true,
    AddDrawingToCalendar: true
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
    "SweepstakesToken": "uuid-v4-string",
    "Group": "allgroups",
    "SelectedAction": 1,
    "ScheduleMode": "schedule",
    "HowManyWinnersToPick": 1,
    "Frequency": 0,
    "EndDate": "2025-02-15",
    "EndTime": "15:00",
    "Timezone": 1,
    "IncludeOptedOutParticipants": False,
    "PublishToWinnersPage": False,
    "SendCopyToMe": False,
    "DoNotIncludeSpamParticipants": True,
    "AddDrawingToCalendar": True
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

**400 Bad Request - Invalid Parameters**
```json
{
  "Response": false,
  "Message": "Invalid parameters in body object, read documentation.",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes",
      "HowManyWinnersToPick": "number (required) \u2014 how many winners to draw. Must be at least 1",
      "Timezone": "number (required) \u2014 TimezoneId. Use /tools/timezones to list them",
      "ScheduleMode": "string (optional) \u2014 how the drawing repeats",
      "Frequency": "string (optional) \u2014 frequency when the drawing repeats",
      "DayOfTheWeek": "string (optional) \u2014 day of the week for a weekly drawing",
      "WeekOfTheMonth": "string (optional) \u2014 week of the month for a monthly drawing",
      "EndDate": "string (optional) \u2014 date of the drawing, YYYY-MM-DD. Must be in the present or future",
      "EndTime": "string (optional) \u2014 time of the drawing, HH:MM (24-hour)",
      "DeliveryTime": "string (optional) \u2014 time the notification is sent, HH:MM (24-hour)",
      "Group": "string (optional) \u2014 UUID v4 of the group to draw from",
      "Winners": "array (optional) \u2014 pre-selected winners",
      "Message": "string (optional) \u2014 message sent to the winners",
      "SelectedAction": "string (optional) \u2014 action taken once the drawing runs",
      "Automation": "boolean (optional) \u2014 run the drawing automatically",
      "PublishToWinnersPage": "boolean (optional) \u2014 publish the winners to the public winners page",
      "AddDrawingToCalendar": "boolean (optional) \u2014 add the drawing to your calendar",
      "SendCopyToMe": "boolean (optional) \u2014 send yourself a copy of the notification",
      "IncludeOptedOutParticipants": "boolean (optional) \u2014 include participants who opted out",
      "DoNotIncludeSpamParticipants": "boolean (optional) \u2014 exclude participants flagged as spam"
    }
  },
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
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes",
      "HowManyWinnersToPick": "number (required) \u2014 how many winners to draw. Must be at least 1",
      "Timezone": "number (required) \u2014 TimezoneId. Use /tools/timezones to list them",
      "ScheduleMode": "string (optional) \u2014 how the drawing repeats",
      "Frequency": "string (optional) \u2014 frequency when the drawing repeats",
      "DayOfTheWeek": "string (optional) \u2014 day of the week for a weekly drawing",
      "WeekOfTheMonth": "string (optional) \u2014 week of the month for a monthly drawing",
      "EndDate": "string (optional) \u2014 date of the drawing, YYYY-MM-DD. Must be in the present or future",
      "EndTime": "string (optional) \u2014 time of the drawing, HH:MM (24-hour)",
      "DeliveryTime": "string (optional) \u2014 time the notification is sent, HH:MM (24-hour)",
      "Group": "string (optional) \u2014 UUID v4 of the group to draw from",
      "Winners": "array (optional) \u2014 pre-selected winners",
      "Message": "string (optional) \u2014 message sent to the winners",
      "SelectedAction": "string (optional) \u2014 action taken once the drawing runs",
      "Automation": "boolean (optional) \u2014 run the drawing automatically",
      "PublishToWinnersPage": "boolean (optional) \u2014 publish the winners to the public winners page",
      "AddDrawingToCalendar": "boolean (optional) \u2014 add the drawing to your calendar",
      "SendCopyToMe": "boolean (optional) \u2014 send yourself a copy of the notification",
      "IncludeOptedOutParticipants": "boolean (optional) \u2014 include participants who opted out",
      "DoNotIncludeSpamParticipants": "boolean (optional) \u2014 exclude participants flagged as spam"
    }
  },
  "Code": 400
}
```

**400 Bad Request - Invalid Winners Count**
```json
{
  "Response": false,
  "Message": "Invalid HowManyWinnersToPick parameter. Must be at least 1.",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes",
      "HowManyWinnersToPick": "number (required) \u2014 how many winners to draw. Must be at least 1",
      "Timezone": "number (required) \u2014 TimezoneId. Use /tools/timezones to list them",
      "ScheduleMode": "string (optional) \u2014 how the drawing repeats",
      "Frequency": "string (optional) \u2014 frequency when the drawing repeats",
      "DayOfTheWeek": "string (optional) \u2014 day of the week for a weekly drawing",
      "WeekOfTheMonth": "string (optional) \u2014 week of the month for a monthly drawing",
      "EndDate": "string (optional) \u2014 date of the drawing, YYYY-MM-DD. Must be in the present or future",
      "EndTime": "string (optional) \u2014 time of the drawing, HH:MM (24-hour)",
      "DeliveryTime": "string (optional) \u2014 time the notification is sent, HH:MM (24-hour)",
      "Group": "string (optional) \u2014 UUID v4 of the group to draw from",
      "Winners": "array (optional) \u2014 pre-selected winners",
      "Message": "string (optional) \u2014 message sent to the winners",
      "SelectedAction": "string (optional) \u2014 action taken once the drawing runs",
      "Automation": "boolean (optional) \u2014 run the drawing automatically",
      "PublishToWinnersPage": "boolean (optional) \u2014 publish the winners to the public winners page",
      "AddDrawingToCalendar": "boolean (optional) \u2014 add the drawing to your calendar",
      "SendCopyToMe": "boolean (optional) \u2014 send yourself a copy of the notification",
      "IncludeOptedOutParticipants": "boolean (optional) \u2014 include participants who opted out",
      "DoNotIncludeSpamParticipants": "boolean (optional) \u2014 exclude participants flagged as spam"
    }
  },
  "Code": 400
}
```

**400 Bad Request - Not Enough Participants**
```json
{
  "Response": false,
  "Message": "Not enough eligible participants. Requested: 5, Available: 3",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes",
      "HowManyWinnersToPick": "number (required) \u2014 how many winners to draw. Must be at least 1",
      "Timezone": "number (required) \u2014 TimezoneId. Use /tools/timezones to list them",
      "ScheduleMode": "string (optional) \u2014 how the drawing repeats",
      "Frequency": "string (optional) \u2014 frequency when the drawing repeats",
      "DayOfTheWeek": "string (optional) \u2014 day of the week for a weekly drawing",
      "WeekOfTheMonth": "string (optional) \u2014 week of the month for a monthly drawing",
      "EndDate": "string (optional) \u2014 date of the drawing, YYYY-MM-DD. Must be in the present or future",
      "EndTime": "string (optional) \u2014 time of the drawing, HH:MM (24-hour)",
      "DeliveryTime": "string (optional) \u2014 time the notification is sent, HH:MM (24-hour)",
      "Group": "string (optional) \u2014 UUID v4 of the group to draw from",
      "Winners": "array (optional) \u2014 pre-selected winners",
      "Message": "string (optional) \u2014 message sent to the winners",
      "SelectedAction": "string (optional) \u2014 action taken once the drawing runs",
      "Automation": "boolean (optional) \u2014 run the drawing automatically",
      "PublishToWinnersPage": "boolean (optional) \u2014 publish the winners to the public winners page",
      "AddDrawingToCalendar": "boolean (optional) \u2014 add the drawing to your calendar",
      "SendCopyToMe": "boolean (optional) \u2014 send yourself a copy of the notification",
      "IncludeOptedOutParticipants": "boolean (optional) \u2014 include participants who opted out",
      "DoNotIncludeSpamParticipants": "boolean (optional) \u2014 exclude participants flagged as spam"
    }
  },
  "Code": 400
}
```

**400 Bad Request - Invalid Schedule Date**
```json
{
  "Response": false,
  "Message": "Invalid schedule date/time. The scheduled drawing must be in the present or future.",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes",
      "HowManyWinnersToPick": "number (required) \u2014 how many winners to draw. Must be at least 1",
      "Timezone": "number (required) \u2014 TimezoneId. Use /tools/timezones to list them",
      "ScheduleMode": "string (optional) \u2014 how the drawing repeats",
      "Frequency": "string (optional) \u2014 frequency when the drawing repeats",
      "DayOfTheWeek": "string (optional) \u2014 day of the week for a weekly drawing",
      "WeekOfTheMonth": "string (optional) \u2014 week of the month for a monthly drawing",
      "EndDate": "string (optional) \u2014 date of the drawing, YYYY-MM-DD. Must be in the present or future",
      "EndTime": "string (optional) \u2014 time of the drawing, HH:MM (24-hour)",
      "DeliveryTime": "string (optional) \u2014 time the notification is sent, HH:MM (24-hour)",
      "Group": "string (optional) \u2014 UUID v4 of the group to draw from",
      "Winners": "array (optional) \u2014 pre-selected winners",
      "Message": "string (optional) \u2014 message sent to the winners",
      "SelectedAction": "string (optional) \u2014 action taken once the drawing runs",
      "Automation": "boolean (optional) \u2014 run the drawing automatically",
      "PublishToWinnersPage": "boolean (optional) \u2014 publish the winners to the public winners page",
      "AddDrawingToCalendar": "boolean (optional) \u2014 add the drawing to your calendar",
      "SendCopyToMe": "boolean (optional) \u2014 send yourself a copy of the notification",
      "IncludeOptedOutParticipants": "boolean (optional) \u2014 include participants who opted out",
      "DoNotIncludeSpamParticipants": "boolean (optional) \u2014 exclude participants flagged as spam"
    }
  },
  "Code": 400
}
```
