# Create Sweepstakes

Create a new sweepstakes with all necessary configurations including entry pages, groups, short links, and default settings.

## Endpoint

`POST sweepstakes/create`

## Description

This endpoint allows you to create a new sweepstakes programmatically. The system validates user plan limits, handler uniqueness, and date constraints before creating the sweepstakes.

When you create a sweepstakes, the system automatically generates:

- **Sweepstakes Record** - Main sweepstakes configuration with default settings
- **Primary Group** - Default participants group for managing entries
- **Entry Page** - Default entry page with form fields (First Name, Last Name, Email, Mobile Number)
- **Social Tracking** - Social shares tracking configuration
- **Coupons Settings** - Coupons distribution and redemption configuration
- **S3 Folder** - Storage folder for entry page images and assets
- **Short Links** - Multiple short links for entry pages, AMOE forms, official rules, and winners pages
- **Calendar Events** - Optional START and END calendar events (if CreateInCalendar is true)
- **QR Code** - Generated QR code (SMSTO: format for SMS, URL for Web/Email/Social)
- **Email Notifications** - Confirmation email with tips and QR code sent to user
- **Winners APP Sync** - Optional sync with Winners APP (if SyncWithWinners is enabled)
- **User Scoring** - +3 points added to user's scoring system

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesName` | String | Required | The name of the sweepstakes. **Maximum 200 characters**; longer values are rejected with a `400`. |
| `SweepstakesType` | Integer | Required | Type of sweepstakes: `1` = SMS, `2` = Email, `3` = Social |
| `Handler` | String | Required | Unique handler/keyword (max 20 chars, alphanumeric only, e.g., "WIN2026") |
| `StartDate` | String | Required | Start date in YYYY-MM-DD format (must be today or future) |
| `EndDate` | String | Required | End date in YYYY-MM-DD format (must be today or future, cannot be before StartDate). Stored internally as `Settings.ExpirationDate` — `/sweepstakes/fetch` returns it as both `Settings.ExpirationDate` and `Settings.EndDate` |
| `StartTime` | String | Required | Start time in HH:MM format (24-hour, default: "00:00") |
| `EndTime` | String | Required | End time in HH:MM format (24-hour, default: "23:59") |
| `CreateInCalendar` | Boolean | Optional | Create START and END calendar events (default: false) |
| `SyncWithWinners` | Boolean | Optional | Sync sweepstakes with Winners APP (default: false) |
| `DeleteIfDeleted` | Boolean | Optional | Delete from Winners APP if sweepstakes is deleted (default: false) |
| `DeleteIfAcctDeleted` | Boolean | Optional | Delete from Winners APP if user account is deleted (default: false) |

> **Note:** The timezone is automatically set from the user's account settings and cannot be specified as a parameter.

## Handler Validation Rules

- **Length:** Maximum 20 characters
- **Characters:** Only alphanumeric (A-Z, 0-9), no spaces or special characters
- **Case:** Automatically converted to uppercase
- **Uniqueness:** Must not already exist in the system
- **Blacklist:** Cannot be a system-reserved keyword

**Valid Examples:**
```
WIN2026
SUMMER
GIVEAWAY123
PRIZE
```

**Invalid Examples:**
```
WIN 2026 (contains space)
SUMMER-GIVEAWAY (contains hyphen)
PRIZE! (contains special character)
VERYLONGHANDLERNAME123 (exceeds 20 characters)
```

## Date Validation Rules

- **StartDate:** Must be today or in the future (cannot be in the past)
- **EndDate:** Must be today or in the future (cannot be in the past)
- **Date Order:** EndDate cannot be earlier than StartDate
- **Date Format:** YYYY-MM-DD (e.g., "2026-06-01")
- **Time Format:** HH:MM in 24-hour format (e.g., "14:30")

## Request Example

```json
{
  "SweepstakesName": "Summer Giveaway 2026",
  "SweepstakesType": 2,
  "Handler": "SUMMER2026",
  "StartDate": "2026-06-01",
  "EndDate": "2026-08-31",
  "StartTime": "00:00",
  "EndTime": "23:59",
  "CreateInCalendar": true,
  "SyncWithWinners": false
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/sweepstakes/create" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesName": "Summer Giveaway 2026",
    "SweepstakesType": 2,
    "Handler": "SUMMER2026",
    "StartDate": "2026-06-01",
    "EndDate": "2026-08-31",
    "StartTime": "00:00",
    "EndTime": "23:59",
    "CreateInCalendar": true,
    "SyncWithWinners": false
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/sweepstakes/create', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesName: "Summer Giveaway 2026",
    SweepstakesType: 2,
    Handler: "SUMMER2026",
    StartDate: "2026-06-01",
    EndDate: "2026-08-31",
    StartTime: "00:00",
    EndTime: "23:59",
    CreateInCalendar: true,
    SyncWithWinners: false
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/sweepstakes/create"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesName": "Summer Giveaway 2026",
    "SweepstakesType": 2,
    "Handler": "SUMMER2026",
    "StartDate": "2026-06-01",
    "EndDate": "2026-08-31",
    "StartTime": "00:00",
    "EndTime": "23:59",
    "CreateInCalendar": True,
    "SyncWithWinners": False
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Message": "Sweepstakes Created Successfully",
  "Data": {
    "SweepstakesToken": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "SweepstakesName": "Summer Giveaway 2026",
    "SweepstakesType": 2,
    "Handler": "SUMMER2026",
    "StartDate": "2026-06-01",
    "EndDate": "2026-08-31",
    "Status": true,
    "CreationDate": "2026-01-27T12:34:56.789Z"
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

**400 Bad Request - Missing Fields**
```json
{
  "Response": false,
  "Message": "Missing Required Fields: SweepstakesName, SweepstakesType, and Handler are required",
  "Code": 400
}
```

**400 Bad Request - Invalid Handler**
```json
{
  "Response": false,
  "Message": "Handler Must Be 20 Characters or Less",
  "Code": 400
}
```

**400 Bad Request - Handler Exists**
```json
{
  "Response": false,
  "Message": "Handler \"SUMMER2026\" Already Exists. Please Choose a Different Handler",
  "Code": 400
}
```

**400 Bad Request - Invalid Date**
```json
{
  "Response": false,
  "Message": "StartDate Cannot Be in the Past. Must Be Today or Later",
  "Code": 400
}
```

**403 Forbidden - Plan Limit**
```json
{
  "Response": false,
  "Message": "Sweepstakes Limit Reached. Your plan allows 5 sweepstakes and you currently have 5. Please upgrade your plan to create more sweepstakes",
  "Code": 403
}
```

## Notes

- **Handler Uniqueness:** The Handler must be unique across all sweepstakes in the system
- **Plan Limits:** The system validates that you haven't exceeded your plan's sweepstakes limit
- **Timezone:** Automatically set from user's account settings (Settings.Locales.Timezone)
- **Form Fields:** Default form fields are created based on SweepstakesType
- **SMS Sweepstakes (Type 1):** Mobile Number is the primary field
- **Email Sweepstakes (Type 2):** Email is the primary field
- **Social Sweepstakes (Type 3):** Email is the primary field
- **Initial Status:** Sweepstakes is created with Status = true (active)
- **Security:** ReCaptcha is enabled by default for spam protection
