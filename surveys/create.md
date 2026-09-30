# Create Survey

Create a survey together with its whole question set in one call.

## Endpoint

`POST /surveys/create`

## Description

A survey always belongs to a **user AND a sweepstakes**, so `SweepstakesToken` is required and is verified against the account that owns the API token. Pagination is implicit: the `Page` you put on each question is a grouping key, not a stored page number. The distinct pages are renumbered `1..N` with no gaps and `Order` is the position inside the array, per page — sending pages 1, 5 and 9 produces pages 1, 2 and 3. Nothing is written unless the **entire** payload validates, so a rejected call never leaves a half-created survey behind.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | String | Yes | UUID v4 of the sweepstakes the survey belongs to |
| `SurveyName` | String | Yes | Survey name, up to 200 characters |
| `Status` | Boolean | No | `false` creates the survey disabled (default: `true`) |
| `Settings` | Object | No | Optional survey settings. `Description` and `ThankYouMessage` (text, max 2000), `QuestionsPerPage` (whole number 1-5), `CollectContactInfo`, `ShowProgressBar`, `ShuffleQuestions`, `AllowMultipleResponses`, `ShowCountdown`, `EnableSharing` (`true`/`false`; `EnableSharing` requires a plan with survey sharing), `RedirectUrl` (an `https://` address, `""` removes it), `StartDate` / `EndDate` (ISO 8601 such as `2026-10-01T09:00:00`, `null` removes it, `EndDate` must be later), `MaxResponses` (whole number, `0` = no limit), `Language` (`en` or `es`) and `Visuals` (see below). Files (`LogoFile`, `Visuals.BackgroundImageFile`, page media) are uploaded in the app: when sent they are not written and are listed in `Data.IgnoredSettings` |
| `Questions` | Array | No | The question set. Each item accepts `Page`, `QuestionText` (required), `QuestionDescription`, `FieldType` (required, one of `text`, `textarea`, `radio`, `checkbox`, `select`, `slider`, `rating`, `nps`, `yesno` or `date`), `Layout`, `Required`, `Options[]` and `Settings{}`. Omit or send `[]` to create an empty survey. Choice questions (`radio`, `checkbox`, `select`) need **2 to 30** distinct `Options` (a string or `{ Label, Value }`); other types take none. `slider` needs `MinValue` lower than `MaxValue`; `checkbox` selection limits cannot exceed its choices. A `text` question with `DataType: "number"` takes an optional `NumberMin` / `NumberMax`, and a `date` question an optional `MinDate` / `MaxDate` (`YYYY-MM-DD`); both are enforced on the public form and when a response is submitted. |

### The look — `Settings.Visuals`

| Key | Accepted values |
|-----|-----------------|
| `PrimaryColor`, `BackgroundColor`, `CardColor`, `TextColor`, `ButtonColor`, `ButtonTextColor` | Hex color: `#RRGGBB` or `#RRGGBBAA` |
| `FontFamily` | `Roboto`, `Arial`, `Georgia`, `Montserrat`, `Poppins`, `Courier New` |
| `ButtonStyle` | `rounded`, `square`, `pill` |
| `TransitionStyle` | `book`, `slide`, `fade` |
| `DarkMode` | `true` / `false` |

Send only the keys you want to change — on an update each one is written on its own, so the rest of the look (and the background image uploaded in the app) stays as it is. The logo and the background image are files and can only be uploaded from the app.

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
    "Availability": {
      "Live": true,
      "Reasons": [],
      "Message": "The public link is live."
    },
    "Status": true,
    "Archived": false,
    "IgnoredSettings": []
  },
  "Message": "Survey Created Successfully. The public link is live."
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
  "Message": "Question 2: a radio question needs at least 2 options (it has 0). A participant could not answer it.",
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

**403 Forbidden** — account-wide plan cap reached (see `MaxSurveysAllowed` in [Plan Details](../account/plan.md)).

```json
{
  "Response": false,
  "Message": "Surveys Limit Reached. Your plan allows 3 survey(s) across the whole account and you currently have 3. Please upgrade your plan to create more surveys",
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
  "Message": "Internal server error. Please try again; if it persists, contact support with the time of this request.",
  "Code": 500
}
```

## Notes

- Structural limits: **5** questions per page (or `Settings.QuestionsPerPage`, whichever is lower), **50** pages, **250** questions and **30** options per question.
- The pages you send are grouping keys. They are renumbered `1..N` with no gaps, and `Order` is assigned from the position inside the array.
- Nothing is written unless every question validates — DocumentDB gives no multi-document transaction to roll back with.
- The survey token and every question token are minted in a **single batch**, so creating a 25-question survey costs two round trips instead of 25 collection scans.
- `Data.Availability` says whether the public link works right now: `Live`, and when it does not, the `Reasons` — `Disabled`, `Archived`, `PlanDoesNotAllowSharing`, `NoQuestions`, `NotStarted`, `Ended`, `MaxResponsesReached`. A survey with no questions shows "Survey Not Available" to visitors; check `Live` before sharing the link. The same sentence is appended to `Message`.
- `Settings.Visuals` sets the look of the public form (colors, font, buttons, transition, dark mode). Files — the logo, the background image and page media — are uploaded in the app only; when sent here they are not written and are listed in `Data.IgnoredSettings`, never silently dropped.
- **Refused, never rewritten.** A value out of range — a choice question without 2-30 options, `QuestionsPerPage: 0`, a text over its limit, a `"true"` string where a boolean is expected, a non-`https` `RedirectUrl`, an `EndDate` before `StartDate` — returns `400` naming the field and the rule, and nothing is written. Earlier versions of this endpoint silently truncated or reset such values.
- The survey is created **enabled** unless `Status: false` is sent, and always unarchived.
- To change the question set afterwards use `POST /surveys/update` — but only while the survey has no responses.
- **🔒 Module Access:** The Surveys module is disabled by default. An administrator must enable it for your account before any of these endpoints will respond.
