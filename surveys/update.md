# Update Survey

Update a survey and, while it has no responses, replace its whole question set.

## Endpoint

`POST /surveys/update`

## Description

Every field is optional — only what you actually send is touched, and settings are written key by key so a partial payload can never clobber the uploaded files and visual styling managed by the app. **`Questions` is a full replacement**: the array you send becomes the whole question set, the previous questions are deleted and new ones are created with fresh tokens. Sending `[]` wipes every question; omitting the field leaves the question set alone. **Structure lock:** a survey with at least one response can no longer have its question set replaced — the answers already collected point at the question tokens a replacement would destroy. The call returns `409` and changes nothing, while `SurveyName`, `Status`, `Archived` and `Settings` stay editable. Purge the collected data from the app, or clone the survey, to unlock the structure.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SurveyToken` | String | Yes | UUID v4 of the survey to update |
| `SurveyName` | String | No | New name, up to 200 characters. Cannot be emptied |
| `Status` | Boolean | No | `true` enables the survey, `false` disables it |
| `Archived` | Boolean | No | `true` archives the survey, `false` restores it |
| `Settings` | Object | No | Optional survey settings. `Description` and `ThankYouMessage` (text, max 2000), `QuestionsPerPage` (whole number 1-5), `CollectContactInfo`, `ShowProgressBar`, `ShuffleQuestions`, `AllowMultipleResponses`, `ShowCountdown`, `EnableSharing` (`true`/`false`; `EnableSharing` requires a plan with survey sharing), `RedirectUrl` (an `https://` address, `""` removes it), `StartDate` / `EndDate` (ISO 8601 such as `2026-10-01T09:00:00`, `null` removes it, `EndDate` must be later), `MaxResponses` (whole number, `0` = no limit), `Language` (`en` or `es`) and `Visuals` (see below). Files (`LogoFile`, `Visuals.BackgroundImageFile`, page media) are uploaded in the app: when sent they are not written and are listed in `Data.IgnoredSettings` |
| `Questions` | Array | No | **Full replacement** of the question set — each item accepts `Page`, `QuestionText` (required), `QuestionDescription`, `FieldType` (required, one of `text`, `textarea`, `radio`, `checkbox`, `select`, `slider`, `rating`, `nps`, `yesno` or `date`), `Layout`, `Required`, `Options[]` and `Settings{}`. Choice questions (`radio`, `checkbox`, `select`) need **2 to 30** distinct `Options` (a string or `{ Label, Value }`); other types take none. `slider` needs `MinValue` lower than `MaxValue`; `checkbox` selection limits cannot exceed its choices. Rejected with `409` when the survey already has responses |

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
  "SurveyToken": "uuid-v4-string",
  "SurveyName": "Post-Purchase Feedback (v2)",
  "Settings": {
    "QuestionsPerPage": 3,
    "ThankYouMessage": "Thanks for your time!"
  },
  "Questions": [
    {
      "Page": 1,
      "QuestionText": "How likely are you to recommend us?",
      "FieldType": "nps"
    },
    {
      "Page": 1,
      "QuestionText": "Would you buy again?",
      "FieldType": "yesno"
    },
    {
      "Page": 2,
      "QuestionText": "Anything else?",
      "FieldType": "textarea"
    }
  ]
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/surveys/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SurveyToken": "uuid-v4-string",
        "SurveyName": "Post-Purchase Feedback (v2)",
        "Settings": {
            "QuestionsPerPage": 3,
            "ThankYouMessage": "Thanks for your time!"
        },
        "Questions": [
            {
                "Page": 1,
                "QuestionText": "How likely are you to recommend us?",
                "FieldType": "nps"
            },
            {
                "Page": 1,
                "QuestionText": "Would you buy again?",
                "FieldType": "yesno"
            },
            {
                "Page": 2,
                "QuestionText": "Anything else?",
                "FieldType": "textarea"
            }
        ]
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/surveys/update', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SurveyToken: "uuid-v4-string",
    SurveyName: "Post-Purchase Feedback (v2)",
    Settings: {
        "QuestionsPerPage": 3,
        "ThankYouMessage": "Thanks for your time!"
    },
    Questions: [
        {
            "Page": 1,
            "QuestionText": "How likely are you to recommend us?",
            "FieldType": "nps"
        },
        {
            "Page": 1,
            "QuestionText": "Would you buy again?",
            "FieldType": "yesno"
        },
        {
            "Page": 2,
            "QuestionText": "Anything else?",
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

url = "https://api-v3.sweeppea.com/surveys/update"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SurveyToken": "uuid-v4-string",
    "SurveyName": "Post-Purchase Feedback (v2)",
    "Settings": {
        "QuestionsPerPage": 3,
        "ThankYouMessage": "Thanks for your time!"
    },
    "Questions": [
        {
            "Page": 1,
            "QuestionText": "How likely are you to recommend us?",
            "FieldType": "nps"
        },
        {
            "Page": 1,
            "QuestionText": "Would you buy again?",
            "FieldType": "yesno"
        },
        {
            "Page": 2,
            "QuestionText": "Anything else?",
            "FieldType": "textarea"
        }
    ]
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
    "SweepstakesName": "Tesla Model 3",
    "SurveyName": "Post-Purchase Feedback (v2)",
    "CreationDate": "2026-07-31T14:02:11.004Z",
    "QuestionsCount": 3,
    "PagesCount": 2,
    "ResponsesCount": 0,
    "IsLocked": false,
    "Settings": {
      "QuestionsPerPage": 3,
      "ThankYouMessage": "Thanks for your time!",
      "LogoFile": null
    },
    "PublicLink": "https://hub.sweeppea.com/s?tkn=uuid-v4-string",
    "Archived": false,
    "Status": true,
    "UpdatedFields": [
      "SurveyName",
      "Settings.QuestionsPerPage",
      "Settings.ThankYouMessage"
    ],
    "QuestionsReplaced": true,
    "IgnoredSettings": []
  },
  "Message": "Survey Updated Successfully"
}
```

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Nothing to update. Supply at least one of: SurveyName, Status, Archived, Settings, Questions.",
  "Code": 400
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Page 1 currently holds 2 questions, so QuestionsPerPage cannot be lowered to 1. Send a new Questions array in the same call to re-lay out the survey.",
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

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Survey not found. It must exist and belong to your account.",
  "Code": 404
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This survey already has 268 response(s), so its question set can no longer be replaced. Purge the collected data from the app first, or clone the survey to start a new question set.",
  "Code": 409,
  "Data": {
    "SurveyToken": "uuid-v4-string",
    "ResponsesCount": 268,
    "IsLocked": true
  }
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

- At least one of `SurveyName`, `Status`, `Archived`, `Settings` or `Questions` must be present, otherwise the call returns `400` instead of reporting a silent no-op.
- Settings are written key by key in dot notation (`Settings.Description`, `Settings.Visuals.PrimaryColor`, …), so the uploaded file objects and the rest of the look are never clobbered by a partial payload.
- **Refused, never rewritten.** A value out of range — a choice question without 2-30 options, `QuestionsPerPage: 0`, a text over its limit, a `"true"` string where a boolean is expected, a non-`https` `RedirectUrl`, an `EndDate` before `StartDate` — returns `400` naming the field and the rule, and nothing is written. Earlier versions of this endpoint silently truncated or reset such values.
- A call whose only content is a key the API does not write (e.g. `Settings.LogoFile`) returns `400` naming it, instead of reporting a no-op success.
- `UpdatedFields` lists exactly what was written. The survey is **read back from the database** after the writes, so the response is the real stored state and not an echo of the request.
- Lowering `QuestionsPerPage` below the current layout is refused with `400` — send a new `Questions` array in the same call to re-lay out the survey.
- Replacing the questions renumbers pages `1..N` with no gaps and reassigns `Order` per page, exactly like `POST /surveys/create`.
- The whole payload is validated before the first write, so bad input can never leave the survey half-updated.
- Collected responses are never touched by this endpoint — which is precisely why the structure lock exists.
- **🔒 Module Access:** The Surveys module is disabled by default. An administrator must enable it for your account before any of these endpoints will respond.
