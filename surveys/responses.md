# Fetch Survey Responses

Retrieve the individual answers collected by a survey, newest first and paginated.

## Endpoint

`POST /surveys/responses`

## Description

This is the raw data behind the report. Every answer carries the question it answers (`QuestionText`, `FieldType`, `Page`), so the payload reads on its own without joining against the question set — which matters after a question set is replaced, because an old response still shows what was actually asked at the time. The `Answer` value is a string, a number or an **array** depending on the field type: a `checkbox` question always yields an array. The device, browser and IP block is opt-in through `IncludeMetadata` because it is personal data.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. The account must also have the **Surveys module enabled** — it is disabled by default and granted by an administrator.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SurveyToken` | String | Yes | UUID v4 of the survey |
| `Completed` | Boolean | No | `true` returns only finished responses, `false` only abandoned ones. Omit to get both |
| `FromDate` | String | No | ISO date (YYYY-MM-DD). Only responses collected on or after this day |
| `ToDate` | String | No | ISO date (YYYY-MM-DD). Only responses collected on or before this day — the whole day is included |
| `IncludeMetadata` | Boolean | No | `true` adds the device / browser / IP block of each response (default: `false`) |
| `Page` | Number | No | Page number (default: 1) |
| `Limit` | Number | No | Results per page (default: 25, maximum: 100) |

## Request Example

```json
{
  "SurveyToken": "uuid-v4-string",
  "Completed": true,
  "FromDate": "2026-08-01",
  "Page": 1,
  "Limit": 50
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/surveys/responses" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SurveyToken": "uuid-v4-string",
        "Completed": true,
        "FromDate": "2026-08-01",
        "Page": 1,
        "Limit": 50
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/surveys/responses', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SurveyToken: "uuid-v4-string",
    Completed: true,
    FromDate: "2026-08-01",
    Page: 1,
    Limit: 50
})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/surveys/responses"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SurveyToken": "uuid-v4-string",
    "Completed": True,
    "FromDate": "2026-08-01",
    "Page": 1,
    "Limit": 50
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
    "SurveyToken": "uuid-v4-string",
    "SweepstakesToken": "uuid-v4-string",
    "SurveyName": "Post-Purchase Feedback",
    "Responses": [
      {
        "ResponseToken": "uuid-v4-string",
        "ParticipantToken": "uuid-v4-string",
        "CreationDate": "2026-08-03T18:22:40.114Z",
        "Completed": true,
        "CompletionTime": 74,
        "Answers": [
          {
            "QuestionToken": "uuid-v4-string",
            "Page": 1,
            "FieldType": "radio",
            "QuestionText": "How did you hear about us?",
            "Answer": "Friend"
          },
          {
            "QuestionToken": "uuid-v4-string",
            "Page": 1,
            "FieldType": "checkbox",
            "QuestionText": "Which products did you buy?",
            "Answer": [
              "Shoes",
              "Socks"
            ]
          }
        ]
      }
    ],
    "Pagination": {
      "Page": 1,
      "Limit": 50,
      "TotalResponses": 268,
      "TotalPages": 6
    }
  },
  "Message": "(OK) Survey responses fetched successfully."
}
```

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: SurveyToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SurveyToken": "string (required) \u2014 UUID v4 of the survey"
    }
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid FromDate or ToDate. Use the ISO format YYYY-MM-DD.",
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

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "The Surveys module is not enabled for this account. Contact support to request access.",
  "Code": 403
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Survey not found. It must exist and belong to your account.",
  "Code": 404
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

- `ParticipantToken` is the participant created from the contact form at the end of the survey, or `null` when contact details were not collected.
- `Completed: false` means the participant abandoned the survey before the submit. `CompletionTime` is the number of seconds between the first interaction and the submit.
- A bare `ToDate` covers the whole day — `2026-08-03` becomes `2026-08-03T23:59:59.999Z`, otherwise everything collected after midnight would silently disappear from the range.
- When `IncludeMetadata` is `true`, each response gains a `Metadata` object with `IpAddress`, `UserAgent`, `Browser`, `DeviceType`, `OperatingSystem`, `ScreenWidth`, `ScreenHeight`, `Language`, `Referrer`, `StartedAt` and `CompletedAt`. Leave it off unless the integration genuinely needs it.
- Results are sorted by `CreationDate` descending, served by the compound index on `SurveyToken` + `CreationDate`.
- For the aggregated view use `POST /surveys/report`. For a spreadsheet export, the app's **Export CSV** button streams every response with one column per question and has no page limit.
- **🔒 Module Access:** The Surveys module is disabled by default. An administrator must enable it for your account before any of these endpoints will respond.
