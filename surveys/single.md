# Fetch Single Survey

Retrieve one survey with its complete question set, ordered by page and position.

## Endpoint

`POST /surveys/single`

## Description

This endpoint returns the full definition of a survey: its settings, its public link, its counters and every question it holds, sorted by `Page` then `Order`. It is the call to make before `POST /surveys/update` — `IsLocked` tells you up front whether a question replacement will be accepted. The whole question set travels in one payload with no pagination; the structural ceiling of 250 questions is small enough for that.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SurveyToken` | String | Yes | UUID v4 of the survey |

## Request Example

```json
{
  "SurveyToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/surveys/single" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SurveyToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/surveys/single', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SurveyToken: "uuid-v4-string"
})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/surveys/single"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SurveyToken": "uuid-v4-string"
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
    "Survey": {
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
    },
    "Questions": [
      {
        "QuestionToken": "uuid-v4-string",
        "Page": 1,
        "Order": 1,
        "QuestionText": "How did you hear about us?",
        "QuestionDescription": "",
        "FieldType": "radio",
        "Layout": "vertical",
        "Options": [
          {
            "Id": "1",
            "Label": "Friend",
            "Value": "Friend",
            "Order": 1
          },
          {
            "Id": "2",
            "Label": "Social media",
            "Value": "Social media",
            "Order": 2
          }
        ],
        "Settings": {
          "Placeholder": "",
          "MaxChars": 500,
          "MinChars": 0,
          "DataType": "text",
          "AllowOther": false,
          "RatingIcon": "star",
          "RatingMax": 5
        },
        "Required": true,
        "Status": true
      }
    ]
  },
  "Message": "(OK) Survey fetched successfully."
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
      "SurveyToken": "string (required) \u2014 UUID v4 of the survey to fetch"
    }
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid SurveyToken. It must be a valid UUID v4 string.",
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

- Ownership is part of the lookup, so another account's survey reads exactly like a survey that does not exist — `404`, never `403`.
- `IsLocked` is `true` when `ResponsesCount &gt; 0`. The question set can then no longer be replaced.
- `Questions` is sorted by `Page` then `Order` and is an empty array for a survey with no questions yet.
- The question set, the response counter and the parent sweepstakes name are fetched in one parallel round trip.
- For the aggregated answers use `POST /surveys/report`; for the individual ones use `POST /surveys/responses`.
- **🔒 Module Access:** The Surveys module is disabled by default. An administrator must enable it for your account before any of these endpoints will respond.
