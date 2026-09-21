# Update Sweepstakes

Update existing sweepstakes name, start date/time, and end date/time. The sweepstakes must belong to the authenticated user.

## Endpoint

`POST sweepstakes/update`

## Description

This endpoint allows you to update specific fields of an existing sweepstakes. The system validates ownership, date constraints, and field limits before applying changes.

**Updatable Fields:**

- **SweepstakesName** - The display name of the sweepstakes (max 200 characters)
- **StartDate** - When the sweepstakes begins accepting entries (YYYY-MM-DD format)
- **EndDate** - When the sweepstakes stops accepting entries (YYYY-MM-DD format)
- **StartTime** - Daily start time for accepting entries (HH:MM 24-hour format)
- **EndTime** - Daily end time for accepting entries (HH:MM 24-hour format)

> **Note:** You must provide at least one field to update. The SweepstakesToken is required to identify which sweepstakes to update.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | Required | Unique identifier of the sweepstakes to update (UUID v4 format) |
| `SweepstakesName` | String | Optional | New name for the sweepstakes (max 200 characters) |
| `StartDate` | String | Optional | New start date in YYYY-MM-DD format (must be today or future) |
| `EndDate` | String | Optional | New end date in YYYY-MM-DD format (must be today or future, cannot be before StartDate) |
| `StartTime` | String | Optional | New start time in HH:MM format (24-hour) |
| `EndTime` | String | Optional | New end time in HH:MM format (24-hour) |

## Date Validation Rules

- **StartDate:** Must be today or in the future (cannot be in the past)
- **EndDate:** Must be today or in the future (cannot be in the past)
- **Date Order:** EndDate cannot be earlier than StartDate
- **Date Format:** YYYY-MM-DD (e.g., "2026-06-01")
- **Time Format:** HH:MM in 24-hour format (e.g., "14:30")

## Request Example

```json
{
  "SweepstakesToken": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "SweepstakesName": "Summer Giveaway 2026 - Extended",
  "StartDate": "2026-06-01",
  "EndDate": "2026-09-30",
  "StartTime": "08:00",
  "EndTime": "20:00"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/sweepstakes/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "SweepstakesName": "Summer Giveaway 2026 - Extended",
    "StartDate": "2026-06-01",
    "EndDate": "2026-09-30",
    "StartTime": "08:00",
    "EndTime": "20:00"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/sweepstakes/update', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    SweepstakesName: "Summer Giveaway 2026 - Extended",
    StartDate: "2026-06-01",
    EndDate: "2026-09-30",
    StartTime: "08:00",
    EndTime: "20:00"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/sweepstakes/update"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "SweepstakesName": "Summer Giveaway 2026 - Extended",
    "StartDate": "2026-06-01",
    "EndDate": "2026-09-30",
    "StartTime": "08:00",
    "EndTime": "20:00"
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Message": "Sweepstakes Updated Successfully",
  "Data": {
    "SweepstakesToken": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "SweepstakesName": "Summer Giveaway 2026 - Extended",
    "SweepstakesType": 2,
    "Handler": "SUMMER2026",
    "StartDate": "2026-06-01",
    "EndDate": "2026-09-30",
    "StartTime": "08:00",
    "EndTime": "20:00",
    "Status": true,
    "CreationDate": "2026-01-27T12:34:56.789Z"
  }
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

**400 Bad Request - Missing Token**
```json
{
  "Response": false,
  "Message": "Missing Required Field: SweepstakesToken is required",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes",
      "SweepstakesName": "string (optional) \u2014 new name, max 200 characters",
      "StartDate": "string (optional) \u2014 new start date, YYYY-MM-DD",
      "EndDate": "string (optional) \u2014 new end date, YYYY-MM-DD",
      "StartTime": "string (optional) \u2014 new start time, HH:MM (24-hour)",
      "EndTime": "string (optional) \u2014 new end time, HH:MM (24-hour)"
    }
  },
  "Code": 400
}
```

**404 Not Found**
```json
{
  "Response": false,
  "Message": "Sweepstakes Not Found or Access Denied",
  "Code": 404
}
```

**400 Bad Request - Name Too Long**
```json
{
  "Response": false,
  "Message": "SweepstakesName Exceeds Maximum Length of 200 Characters",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes",
      "SweepstakesName": "string (optional) \u2014 new name, max 200 characters",
      "StartDate": "string (optional) \u2014 new start date, YYYY-MM-DD",
      "EndDate": "string (optional) \u2014 new end date, YYYY-MM-DD",
      "StartTime": "string (optional) \u2014 new start time, HH:MM (24-hour)",
      "EndTime": "string (optional) \u2014 new end time, HH:MM (24-hour)"
    }
  },
  "Code": 400
}
```

**400 Bad Request - Invalid Date**
```json
{
  "Response": false,
  "Message": "StartDate Cannot Be in the Past. Must Be Today or Later",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes",
      "SweepstakesName": "string (optional) \u2014 new name, max 200 characters",
      "StartDate": "string (optional) \u2014 new start date, YYYY-MM-DD",
      "EndDate": "string (optional) \u2014 new end date, YYYY-MM-DD",
      "StartTime": "string (optional) \u2014 new start time, HH:MM (24-hour)",
      "EndTime": "string (optional) \u2014 new end time, HH:MM (24-hour)"
    }
  },
  "Code": 400
}
```

**400 Bad Request - Date Order**
```json
{
  "Response": false,
  "Message": "EndDate Cannot Be Earlier Than StartDate",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes",
      "SweepstakesName": "string (optional) \u2014 new name, max 200 characters",
      "StartDate": "string (optional) \u2014 new start date, YYYY-MM-DD",
      "EndDate": "string (optional) \u2014 new end date, YYYY-MM-DD",
      "StartTime": "string (optional) \u2014 new start time, HH:MM (24-hour)",
      "EndTime": "string (optional) \u2014 new end time, HH:MM (24-hour)"
    }
  },
  "Code": 400
}
```

**400 Bad Request - Invalid Time**
```json
{
  "Response": false,
  "Message": "Invalid StartTime Format. Use HH:MM (24-hour format)",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes",
      "SweepstakesName": "string (optional) \u2014 new name, max 200 characters",
      "StartDate": "string (optional) \u2014 new start date, YYYY-MM-DD",
      "EndDate": "string (optional) \u2014 new end date, YYYY-MM-DD",
      "StartTime": "string (optional) \u2014 new start time, HH:MM (24-hour)",
      "EndTime": "string (optional) \u2014 new end time, HH:MM (24-hour)"
    }
  },
  "Code": 400
}
```

**400 Bad Request - No Fields**
```json
{
  "Response": false,
  "Message": "No Fields to Update",
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID v4 of the sweepstakes",
      "SweepstakesName": "string (optional) \u2014 new name, max 200 characters",
      "StartDate": "string (optional) \u2014 new start date, YYYY-MM-DD",
      "EndDate": "string (optional) \u2014 new end date, YYYY-MM-DD",
      "StartTime": "string (optional) \u2014 new start time, HH:MM (24-hour)",
      "EndTime": "string (optional) \u2014 new end time, HH:MM (24-hour)"
    }
  },
  "Code": 400
}
```

## Notes

- **Ownership Validation:** The sweepstakes must belong to the authenticated user
- **Partial Updates:** You can update one or more fields in a single request
- **Immutable Fields:** Handler, SweepstakesType, and other core settings cannot be changed after creation
- **Date Validation:** All date validations are performed before applying changes
- **Atomic Operation:** All changes are applied together or none at all
- **Response Data:** Returns the updated sweepstakes data including unchanged fields
