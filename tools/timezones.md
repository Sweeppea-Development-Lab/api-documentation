# Fetch Timezones

Retrieve all available timezones with their UTC offsets and abbreviations.

## Endpoint

`POST /tools/timezones`

## Description

This endpoint retrieves all timezones available in the system. Each timezone includes its ID, name, abbreviation, UTC offset, display text, and associated UTC identifiers. This is useful for displaying timezone selectors in your application or converting times between different zones.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tools/timezones" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tools/timezones', {
  method: 'POST',
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

url = "https://api-v3.sweeppea.com/tools/timezones"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

response = requests.post(url, headers=headers)
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Data": {
    "TotalTimezones": 8,
    "Timezones": [
      {
        "_id": "5fb8c3e8f1d2a40017a1b2c3",
        "TimezoneId": 1,
        "Timezone": "UTC",
        "Abbreviation": "UTC",
        "OffSet": "+0000",
        "Text": "(UTC) Coordinated Universal Time",
        "Utc": [
          "Etc/GMT",
          "Etc/UTC"
        ]
      },
      {
        "_id": "5fb8c3e8f1d2a40017a1b2c4",
        "TimezoneId": 2,
        "Timezone": "America/New_York",
        "Abbreviation": "EST",
        "OffSet": "-0500",
        "Text": "(UTC-05:00) Eastern Time (US & Canada)",
        "Utc": [
          "America/New_York",
          "America/Detroit"
        ]
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

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `TotalTimezones` | Number | Total number of timezones available |
| `TimezoneId` | Number | Unique numeric identifier for the timezone |
| `Timezone` | String | IANA timezone identifier (e.g., "America/New_York") |
| `Abbreviation` | String | Timezone abbreviation (e.g., "EST", "PST") |
| `OffSet` | String | UTC offset in format +/-HHMM (e.g., "-0500", "+0100") |
| `Text` | String | Human-readable timezone description |
| `Utc` | Array | Array of UTC identifiers associated with this timezone |

## Notes

- **Sorting:** Timezones are sorted by `TimezoneId` in ascending order.
- **IANA Format:** The `Timezone` field uses standard IANA timezone identifiers, compatible with most programming languages and libraries.
- **UTC Identifiers:** The `Utc` array contains alternative identifiers that map to the same timezone.
- **Display Text:** Use the `Text` field for user-friendly timezone selection in dropdowns or forms.
- **Offset Format:** The `OffSet` field uses a 4-digit format (+/-HHMM) for easy parsing and calculations.

## Common Use Cases

- **Timezone Selector:** Populate a dropdown menu with available timezones for user selection in account settings or event scheduling.
- **Time Conversion:** Use timezone offsets to convert times between different zones when displaying sweepstakes schedules or deadlines.
