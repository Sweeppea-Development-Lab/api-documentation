# Delete Survey

Permanently delete a survey with its questions, responses, statistics and uploaded media.

## Endpoint

`POST /surveys/delete`

## Description

This endpoint removes the survey document and **everything attached to it**: its questions, its collected responses, its statistics and the media uploaded to S3 under `{UserToken}/surveys/{SurveyToken}`. **This cannot be undone.** A survey that already holds responses is only deleted when `Confirm: true` is sent explicitly, so a wrong token in an integration never silently destroys collected data. Participants created from a survey are **not** removed — they belong to the sweepstakes, not to the survey.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. The account must also have the **Surveys module enabled** — it is disabled by default and granted by an administrator.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SurveyToken` | String | Yes | UUID v4 of the survey to delete |
| `Confirm` | Boolean | Conditional | Required **only** when the survey already has responses. Must be `true` |

## Request Example

```json
{
  "SurveyToken": "uuid-v4-string",
  "Confirm": true
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/surveys/delete" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "SurveyToken": "uuid-v4-string",
        "Confirm": true
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/surveys/delete', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SurveyToken: "uuid-v4-string",
    Confirm: true
})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/surveys/delete"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SurveyToken": "uuid-v4-string",
    "Confirm": True
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
    "Deleted": {
      "Questions": 3,
      "Responses": 268,
      "Stats": 1420,
      "Files": 2
    }
  },
  "Message": "Survey Deleted Successfully"
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
      "SurveyToken": "string (required) \u2014 UUID v4 of the survey to delete",
      "Confirm": "boolean (required only when the survey already has responses)"
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

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This survey holds 268 collected response(s). Send Confirm: true to delete the survey together with every response and statistic. This cannot be undone.",
  "Code": 409,
  "Data": {
    "SurveyToken": "uuid-v4-string",
    "SurveyName": "Post-Purchase Feedback",
    "ResponsesCount": 268
  }
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

- Removed: the `surveys` document, every `surveysquestions`, `surveysresponses` and `surveysstats` row of that survey, and the S3 folder `{UserToken}/surveys/{SurveyToken}/`.
- Kept: the `participants` created from the survey — they belong to the sweepstakes — and the sweepstakes itself.
- The survey document is deleted first. Once it is gone the survey can no longer be rendered or answered, so an error while cleaning up the children cannot leave a live survey pointing at deleted questions.
- S3 cleanup is best effort: the prefix is listed page by page and each page is deleted in one batched call. An S3 hiccup is logged and never turns into a half-deleted survey.
- There is no bulk delete — loop this endpoint.
- **Deleting is not disabling.** To take a survey offline while keeping its data, use `POST /surveys/update` with `Status: false` or `Archived: true`.
- **🔒 Module Access:** The Surveys module is disabled by default. An administrator must enable it for your account before any of these endpoints will respond.
