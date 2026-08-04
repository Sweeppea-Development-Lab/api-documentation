# Create Survey

Create a survey together with its whole question set in one call.

## Endpoint

`POST /surveys/create`

## Description

A survey always belongs to a **user AND a sweepstakes**, so `SweepstakesToken` is required and is verified against the account that owns the API token. Pagination is implicit: the `Page` you put on each question is a grouping key, not a stored page number. The distinct pages are renumbered `1..N` with no gaps and `Order` is the position inside the array, per page — sending pages 1, 5 and 9 produces pages 1, 2 and 3. Nothing is written unless the **entire** payload validates, so a rejected call never leaves a half-created survey behind.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. The account must also have the **Surveys module enabled** — it is disabled by default and granted by an administrator.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | Yes | UUID v4 of the sweepstakes the survey belongs to |
| `SurveyName` | String | Yes | Survey name, up to 200 characters |
| `Status` | Boolean | No | `false` creates the survey disabled (default: `true`) |
| `Settings` | Object | No | Optional survey settings. Accepted keys: `Description`, `QuestionsPerPage` (1-5), `CollectContactInfo`, `ShowProgressBar`, `ShuffleQuestions`, `AllowMultipleResponses`, `ThankYouMessage`, `RedirectUrl`, `StartDate`, `EndDate`, `MaxResponses`, `ShowCountdown`, `EnableSharing`, `Language` (`en` or `es`). Visual keys (`Visuals`, `Pages`, `LogoFile`) are managed in the app and ignored here |
| `Questions` | Array | No | The question set. Each item accepts `Page`, `QuestionText` (required), `QuestionDescription`, `FieldType` (required, one of `text`, `textarea`, `radio`, `checkbox`, `select`, `slider`, `rating`, `nps`, `yesno` or `date`), `Layout`, `Required`, `Options[]` and `Settings{}`. Omit or send `[]` to create an empty survey |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "SurveyName": "Post-Purchase Feedback",
  "Settings": {
    "QuestionsPerPage": 2,
    "ShowProgressBar": true,
    "Language": "en"
  },
  "Questions": [
    {
      "Page": 1,
      "QuestionText": "How did you hear about us?",
      "FieldType": "radio",
      "Required": true,
      "Options": [
        {
          "Label": "Friend"
        },
        {
          "Label": "Social media"
        }
      ]
    },
    {
      "Page": 1,
      "QuestionText": "Rate our checkout",
      "FieldType": "rating",
      "Settings": {
        "RatingMax": 5,
        "RatingIcon": "star"
      }
    },
    {
      "Page": 2,
      "QuestionText": "Anything we should improve?",
      "FieldType": "textarea"
    }
  ]
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/surveys/create" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SweepstakesToken": "uuid-v4-string",
        "SurveyName": "Post-Purchase Feedback",
        "Settings": {
            "QuestionsPerPage": 2,
            "ShowProgressBar": true,
            "Language": "en"
        },
        "Questions": [
            {
                "Page": 1,
                "QuestionText": "How did you hear about us?",
                "FieldType": "radio",
                "Required": true,
                "Options": [
                    {
                        "Label": "Friend"
                    },
                    {
                        "Label": "Social media"
                    }
                ]
            },
            {
                "Page": 1,
                "QuestionText": "Rate our checkout",
                "FieldType": "rating",
                "Settings": {
                    "RatingMax": 5,
                    "RatingIcon": "star"
                }
            },
            {
                "Page": 2,
                "QuestionText": "Anything we should improve?",
                "FieldType": "textarea"
            }
        ]
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/surveys/create', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "uuid-v4-string",
    SurveyName: "Post-Purchase Feedback",
    Settings: {
        "QuestionsPerPage": 2,
        "ShowProgressBar": true,
        "Language": "en"
    },
    Questions: [
        {
            "Page": 1,
            "QuestionText": "How did you hear about us?",
            "FieldType": "radio",
            "Required": true,
            "Options": [
                {
                    "Label": "Friend"
                },
                {
                    "Label": "Social media"
                }
            ]
        },
        {
            "Page": 1,
            "QuestionText": "Rate our checkout",
            "FieldType": "rating",
            Settings: {
                "RatingMax": 5,
                "RatingIcon": "star"
            }
        },
        {
            "Page": 2,
            "QuestionText": "Anything we should improve?",
            "FieldType": "textarea"
        }
    ]
})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/surveys/create"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "SurveyName": "Post-Purchase Feedback",
    "Settings": {
        "QuestionsPerPage": 2,
        "ShowProgressBar": True,
        "Language": "en"
    },
    "Questions": [
        {
            "Page": 1,
            "QuestionText": "How did you hear about us?",
            "FieldType": "radio",
            "Required": True,
            "Options": [
                {
                    "Label": "Friend"
                },
                {
                    "Label": "Social media"
                }
            ]
        },
        {
            "Page": 1,
            "QuestionText": "Rate our checkout",
            "FieldType": "rating",
            "Settings": {
                "RatingMax": 5,
                "RatingIcon": "star"
            }
        },
        {
            "Page": 2,
            "QuestionText": "Anything we should improve?",
            "FieldType": "textarea"
        }
    ]
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**201 Created**

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
    "SweepstakesName": "Tesla Model 3",
    "SurveyName": "Post-Purchase Feedback",
    "QuestionsCount": 3,
    "PagesCount": 2,
    "Settings": {
      "QuestionsPerPage": 2,
      "ShowProgressBar": true,
      "Language": "en",
      "LogoFile": null
    },
    "PublicLink": "https://hub.sweeppea.com/s?tkn=uuid-v4-string",
    "Status": true,
    "Archived": false
  },
  "Message": "Survey Created Successfully"
}
```

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: SweepstakesToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "SweepstakesToken": "string (required) \u2014 UUID of the sweepstakes",
      "SurveyName": "string (required) \u2014 Name of the survey"
    }
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Question at position 2 is invalid. QuestionText is required and FieldType must be one of: text, textarea, radio, checkbox, select, slider, rating, nps, yesno, date.",
  "Code": 400
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Page 1 holds 3 questions but the maximum is 2. Adjust Settings.QuestionsPerPage or move questions to another page.",
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
  "Message": "Sweepstakes not found. It must exist and belong to your account.",
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

- Structural limits: **5** questions per page (or `Settings.QuestionsPerPage`, whichever is lower), **50** pages, **250** questions and **30** options per question.
- The pages you send are grouping keys. They are renumbered `1..N` with no gaps, and `Order` is assigned from the position inside the array.
- Nothing is written unless every question validates — DocumentDB gives no multi-document transaction to roll back with.
- The survey token and every question token are minted in a **single batch**, so creating a 25-question survey costs two round trips instead of 25 collection scans.
- Visual settings (`Visuals`, `Pages`, `LogoFile`) are silently ignored — they are uploaded and styled in the app, and accepting them here would let an integration clobber an uploaded file object.
- The survey is created **enabled** unless `Status: false` is sent, and always unarchived.
- To change the question set afterwards use `POST /surveys/update` — but only while the survey has no responses.
- **🔒 Module Access:** The Surveys module is disabled by default. An administrator must enable it for your account before any of these endpoints will respond.
