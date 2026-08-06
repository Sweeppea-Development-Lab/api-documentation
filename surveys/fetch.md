# Fetch Surveys

List the surveys of the authenticated account with their question, page and response counters.

## Endpoint

`POST /surveys/fetch`

## Description

This endpoint returns a paginated list of surveys, newest first, together with the counters needed to decide what to do next: how many questions each survey holds, how many pages it spans and how many responses it has collected. Every row carries the **public link** rendered by the HUB, so a listing is enough to distribute a survey. The `IsLocked` flag is derived at read time and never stored — a survey is locked once it has at least one response, and from that moment its question set can no longer be replaced through `/surveys/update`. The counters are aggregated for the returned page only, so the call cost does not grow with the size of the account.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | No | UUID v4. Only the surveys of that sweepstakes |
| `Status` | Boolean | No | `true` returns enabled surveys, `false` disabled ones. Omit to get both |
| `Archived` | Boolean | No | Filter by the archived flag. Omit to get both |
| `Page` | Number | No | Page number (default: 1) |
| `Limit` | Number | No | Results per page (default: 50, maximum: 200) |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "Status": true,
  "Page": 1,
  "Limit": 25
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/surveys/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SweepstakesToken": "uuid-v4-string",
        "Status": true,
        "Page": 1,
        "Limit": 25
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/surveys/fetch', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "uuid-v4-string",
    Status: true,
    Page: 1,
    Limit: 25
})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/surveys/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "Status": True,
    "Page": 1,
    "Limit": 25
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 204,
    "MaxAPICalls": 1500000
  },
  "Data": {
    "Surveys": [
      {
        "SurveyToken": "uuid-v4-string",
        "SweepstakesToken": "uuid-v4-string",
        "SweepstakesName": "Tesla Model 3",
        "SurveyName": "Post-Purchase Feedback",
        "CreationDate": "2026-07-31T14:02:11.004Z",
        "QuestionsCount": 3,
        "PagesCount": 2,
        "ResponsesCount": 268,
        "IsLocked": true,
        "Settings": {
          "QuestionsPerPage": 2,
          "ShowProgressBar": true,
          "Language": "en",
          "LogoFile": null
        },
        "PublicLink": "https://hub.sweeppea.com/s?tkn=uuid-v4-string",
        "Archived": false,
        "Status": true
      }
    ],
    "Pagination": {
      "Page": 1,
      "Limit": 25,
      "TotalSurveys": 1,
      "TotalPages": 1
    }
  },
  "Message": "(OK) Surveys fetched successfully."
}
```

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid SweepstakesToken. It must be a valid UUID v4 string.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (optional) \u2014 UUID v4. Only surveys of that sweepstakes"
    }
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

## Notes

- Every filter is optional — an empty body returns the whole account, 50 rows at a time.
- Results are always scoped to the account that owns the API token. Another account's surveys are never visible.
- `PagesCount` is the highest page actually holding a question, not the number of pages configured.
- `Settings` never carries the base64 buffer of an uploaded file, and `Visuals` is omitted entirely — styling lives in the app.
- `PublicLink` is the HUB address the participant answers on: `https://hub.sweeppea.com/s?tkn={SurveyToken}`.
- For one survey **with its questions**, use `POST /surveys/single`.
- **🔒 Module Access:** The Surveys module is disabled by default. An administrator must enable it for your account before any of these endpoints will respond.
