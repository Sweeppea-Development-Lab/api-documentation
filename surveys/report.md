# Fetch Survey Report

Retrieve the full aggregated report of a survey — funnel, completion metrics, breakdowns and answer distributions.

## Endpoint

`POST /surveys/report`

## Description

This endpoint returns the same payload the Reports screen draws its charts from: the visit → start → complete funnel, the completion rate and times, the device and browser breakdowns, a daily timeline and the answer distribution of every question. Array answers are unwound twice, so every selected checkbox counts on its own instead of the whole selection counting as one opaque bucket. Every preset value is listed in its own order with its count — **zeros included**, so an option nobody picked is still on the chart — and `Respondents` (the people who answered) travels next to `TotalSelections` (a checkbox counts every tick). Written-in "Other" answers follow, most frequent first with a stable tie-breaker, up to 25; the rest is summarised in `OtherHidden` and `Truncated` says when that happened. A survey with no traffic returns a fully shaped report of zeros, never an error.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SurveyToken` | String | Yes | UUID v4 of the survey |
| `TimelineDays` | Number | No | How many days back the daily timeline reaches (default: 30, maximum: 365) |

## Request Example

```json
{
  "SurveyToken": "uuid-v4-string",
  "TimelineDays": 90
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/surveys/report" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SurveyToken": "uuid-v4-string",
        "TimelineDays": 90
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/surveys/report', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SurveyToken: "uuid-v4-string",
    TimelineDays: 90
})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/surveys/report"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SurveyToken": "uuid-v4-string",
    "TimelineDays": 90
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
    "CreationDate": "2026-07-31T14:02:11.004Z",
    "TimelineDays": 90,
    "AIInsights": null,
    "Report": {
      "Totals": {
        "Visits": 420,
        "Starts": 310,
        "PageViews": 690,
        "Completes": 268,
        "Abandons": 42
      },
      "TotalResponses": 268,
      "CompletedResponses": 268,
      "AbandonedResponses": 0,
      "CompletionRate": 64,
      "CompletionTimes": {
        "Avg": 74,
        "Min": 21,
        "Max": 402
      },
      "Devices": [
        {
          "Label": "mobile",
          "Count": 301
        },
        {
          "Label": "desktop",
          "Count": 119
        }
      ],
      "Browsers": [
        {
          "Label": "Chrome",
          "Count": 244
        },
        {
          "Label": "Safari",
          "Count": 176
        }
      ],
      "Timeline": [
        {
          "Day": "2026-08-01",
          "EventType": "visit",
          "Count": 38
        }
      ],
      "Questions": [
        {
          "QuestionToken": "uuid-v4-string",
          "Page": 1,
          "Order": 1,
          "QuestionText": "How did you hear about us?",
          "FieldType": "radio",
          "Options": [
            {
              "Id": "1",
              "Label": "Friend",
              "Value": "Friend",
              "Order": 1
            }
          ],
          "Distribution": [
            {
              "Answer": "Friend",
              "Count": 140,
              "Kind": "value"
            },
            {
              "Answer": "Social media",
              "Count": 128,
              "Kind": "value"
            },
            {
              "Answer": "Newspaper",
              "Count": 0,
              "Kind": "value"
            }
          ],
          "TotalAnswers": 268,
          "Respondents": 268,
          "TotalSelections": 268,
          "OtherHidden": {
            "Answers": 0,
            "Count": 0
          },
          "Truncated": false
        }
      ]
    }
  },
  "Message": "Survey report fetched successfully."
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
      "SurveyToken": "string (required) \u2014 UUID v4 of the survey",
      "TimelineDays": "number (optional) \u2014 Days back for the timeline, max 365"
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

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal server error. Please try again; if it persists, contact support with the time of this request.",
  "Code": 500
}
```

## Notes

- `Totals` is the funnel counted from the statistics event log: `visit` → `start` → `pageview` → `complete` / `abandon`.
- `CompletionRate` is `Completes / Visits` rounded to a whole percent, and is `0` when there were no visits. `CompletionTimes` is in seconds.
- `TotalResponses` counts every stored response including the abandoned ones; `CompletedResponses` and `AbandonedResponses` split them by the completion flag.
- `Devices` and `Browsers` are counted from `visit` events only. `Unknown` groups the visits that reported nothing.
- `Timeline` has one row per day **and** event type, ascending. Days with no activity are absent.
- `Distribution` lists the question's own values first, in their order and **including zeros**: the options of a choice question, `Yes`/`No`, every score of a rating (1 to `RatingMax`) or NPS scale. `Kind` is `value` for those (and a slider's values, ascending) and `other` for written-in "Other" answers, which follow most frequent first (ties by text) up to 25; `OtherHidden` counts the rest.
- `Respondents` is the number of people who answered the question — the denominator of a share. `TotalSelections` (and `TotalAnswers`, kept for compatibility) is the sum of the buckets: on a `checkbox` it counts every tick, so it can exceed `Respondents`.
- `AIInsights` carries the last insights generated from the app, when any. The API never triggers the model itself.
- All aggregations run in parallel rather than inside a `$facet` — DocumentDB does not support it.
- For the raw answers behind these numbers use `POST /surveys/responses`.
- **🔒 Module Access:** The Surveys module is disabled by default. An administrator must enable it for your account before any of these endpoints will respond.
