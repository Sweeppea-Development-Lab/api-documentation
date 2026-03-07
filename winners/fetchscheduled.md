# Fetch Scheduled Drawings

Retrieve all scheduled winner drawings for a specific sweepstakes.

## Endpoint

`GET /winners/fetchscheduled`

## Description

This endpoint allows you to fetch all scheduled drawings for a specific sweepstakes. It returns detailed information about each scheduled drawing including timing, frequency, number of winners, and current status.

**Important Notes:**

- The sweepstakes must belong to the authenticated user
- Results are sorted by creation date (newest first)
- Includes all scheduled drawings regardless of status

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sweepstakesToken` | String (UUID v4) | Required | The unique identifier of the sweepstakes (query string parameter) |

## Code Examples

### cURL

```bash
curl -X GET "https://api-v3.sweeppea.com/winners/fetchscheduled?sweepstakesToken=uuid-v4-string" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/winners/fetchscheduled?sweepstakesToken=uuid-v4-string', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY'
  }
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/winners/fetchscheduled"
headers = {
    "Authorization": "Bearer YOUR_API_KEY"
}
params = {
    "sweepstakesToken": "uuid-v4-string"
}

response = requests.get(url, headers=headers, params=params)
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Drawings": [
    {
      "ScheduleToken": "uuid-v4-string",
      "CreationDate": "2026-02-16T13:27:19.206Z",
      "Action": 1,
      "ScheduleMode": 2,
      "Frequency": 0,
      "HowManyWinnersToPick": 1,
      "DayOfTheWeek": 0,
      "WeekOfTheMonth": 0,
      "DrawingDate": "2025-02-20T00:00:00.000Z",
      "DrawingTime": "15:00",
      "DeliveryTime": "",
      "DrawingEndDate": null,
      "DrawingEndTime": "",
      "Timezone": "1",
      "TimezoneDescription": "Eastern Time (US & Canada)",
      "GroupToken": "allgroups",
      "GroupName": "All Groups",
      "Message": null,
      "Options": {
        "IncludeOptedOutParticipants": false,
        "PublishToWinnersPage": false,
        "SendCopyToMe": false,
        "DoNotIncludeSpamParticipants": true
      },
      "Status": 0,
      "StatusDescription": "Pending"
    }
  ],
  "Total": 1
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
  "Message": "Invalid API token",
  "Code": 403
}
```

**400 Bad Request**
```json
{
  "Response": false,
  "Message": "Missing sweepstakesToken query parameter",
  "Code": 400
}
```

**404 Not Found**
```json
{
  "Response": false,
  "Message": "Sweepstakes not found",
  "Code": 404
}
```

**403 Forbidden - No Permission**
```json
{
  "Response": false,
  "Message": "You do not have permission to access this sweepstakes",
  "Code": 403
}
```
