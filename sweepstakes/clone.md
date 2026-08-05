# Clone Sweepstakes

Clone an existing sweepstakes with new parameters, creating a complete copy with new tokens and specified dates.

## Endpoint

`POST sweepstakes/clone`

## Description

This endpoint allows you to clone an existing sweepstakes with new parameters. Creates a complete copy including entry pages, calendar events, short links, groups, and all configurations. The cloned sweepstakes will have new tokens and the specified dates/times while preserving all settings from the original.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `Handler` | String | Required | The handler of the original sweepstakes to clone |
| `HandlerNew` | String | Required | The handler for the new cloned sweepstakes |
| `SweepstakesName` | String | Required | Name for the new cloned sweepstakes |
| `StartDate` | String | Required | Start date for the clone in YYYY-MM-DD format |
| `EndDate` | String | Required | End date for the clone in YYYY-MM-DD format |
| `StartTime` | String | Required | Start time for the clone in HH:MM format |
| `EndTime` | String | Required | End time for the clone in HH:MM format |

## Request Example

```json
{
  "Handler": "ORIGINALHANDLER",
  "HandlerNew": "NEWHANDLER",
  "SweepstakesName": "New Sweepstakes Name",
  "StartDate": "2025-12-01",
  "EndDate": "2025-12-31",
  "StartTime": "09:00",
  "EndTime": "23:59"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/sweepstakes/clone" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Handler": "ORIGINALHANDLER",
    "HandlerNew": "NEWHANDLER",
    "SweepstakesName": "New Sweepstakes Name",
    "StartDate": "2025-12-01",
    "EndDate": "2025-12-31",
    "StartTime": "09:00",
    "EndTime": "23:59"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/sweepstakes/clone', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    'Handler': 'ORIGINALHANDLER',
    'HandlerNew': 'NEWHANDLER',
    'SweepstakesName': 'New Sweepstakes Name',
    'StartDate': '2025-12-01',
    'EndDate': '2025-12-31',
    'StartTime': '09:00',
    'EndTime': '23:59'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/sweepstakes/clone"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "Handler": "ORIGINALHANDLER",
    "HandlerNew": "NEWHANDLER",
    "SweepstakesName": "New Sweepstakes Name",
    "StartDate": "2025-12-01",
    "EndDate": "2025-12-31",
    "StartTime": "09:00",
    "EndTime": "23:59"
}

response = requests.post(url, headers=headers, data=json.dumps(data))
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "SweepstakesToken": "uuid-v4-string",
  "SweepstakesType": 2,
  "SweepstakesName": "New Sweepstakes Name",
  "Handler": "NEWHANDLER",
  "StartDate": "2025-12-01",
  "StartTime": "09:00",
  "EndDate": "2025-12-31",
  "EndTime": "23:59",
  "Status": "active",
  "Message": "Sweepstakes successfully cloned."
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

**403 Forbidden** — plan cap reached. A clone consumes a sweepstakes slot exactly like a creation, and the count includes archived sweepstakes (see `MaxSweepstakesAllowed` in [Plan Details](../account/plan.md)).
```json
{
  "Response": false,
  "Message": "Sweepstakes Limit Reached. Your plan allows 10 sweepstakes and you currently have 10. Please upgrade your plan to create more sweepstakes",
  "Code": 403
}
```

**400 Bad Request**
```json
{
  "Response": false,
  "Message": "Missing required parameters or validation errors",
  "Code": 400
}
```

**404 Not Found**
```json
{
  "Response": false,
  "Message": "User or original sweepstakes not found",
  "Code": 404
}
```

**409 Conflict**
```json
{
  "Response": false,
  "Message": "New handler already exists",
  "Code": 409
}
```
