# Count Participants

Count participants in a sweepstakes with optional type and date range filters.

## Endpoint

`POST /participants/count`

## Description

This endpoint allows you to count participants in a sweepstakes. You can filter by participant type (participants, AMOE participants, opt-outs, or all) and apply date range filters. Use this to get statistics about your sweepstakes participants programmatically through the Sweeppea API.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter          | Type             | Required | Description                                                                          |
|--------------------|------------------|----------|--------------------------------------------------------------------------------------|
| `SweepstakesToken` | String (UUID v4) | Yes      | Unique identifier for the sweepstakes                                                |
| `FilterType`       | String           | No       | Type of count: `"all"`, `"participants"`, `"amoe"`, `"optouts"` (default: `"all"`)  |
| `StartDate`        | String (ISO 8601) | No      | Start date for filtering participants (e.g., `"2025-01-01"`)                         |
| `EndDate`          | String (ISO 8601) | No      | End date for filtering participants (e.g., `"2025-12-31"`)                           |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "FilterType": "all",
  "StartDate": "2025-01-01",
  "EndDate": "2025-12-31"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/participants/count" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "uuid-v4-string",
    "FilterType": "all",
    "StartDate": "2025-01-01",
    "EndDate": "2025-12-31"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/participants/count', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: 'uuid-v4-string',
    FilterType: 'all',
    StartDate: '2025-01-01',
    EndDate: '2025-12-31'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/participants/count"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "FilterType": "all",
    "StartDate": "2025-01-01",
    "EndDate": "2025-12-31"
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "SweepstakesToken": "uuid-v4-string",
    "FilterType": "all",
    "DateRange": {
      "StartDate": "2025-01-01",
      "EndDate": "2025-12-31"
    },
    "Counts": {
      "Participants": 1250,
      "AmoeParticipants": 85,
      "OptOuts": 42,
      "Total": 1377
    }
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid or Missing SweepstakesToken",
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

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Sweepstakes Not Found or Access Denied",
  "Code": 404
}
```

## Filter Type Examples

**Count Only Participants:**

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "FilterType": "participants"
}
```

**Count Only AMOE Participants:**

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "FilterType": "amoe"
}
```

**Count Only Opt-Outs:**

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "FilterType": "optouts"
}
```
