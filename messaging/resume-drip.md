# Resume Drip Campaign

Resumes a drip campaign you paused, provided nothing has changed since and the account can still send.

## Endpoint

`POST /messaging/resume-drip`

## Description

Only a `paused` drip that has not been edited since it was paused can be resumed through the API. The same checks as the app run first: your plan lets drips run and includes the channel, an SMS drip has a registered 10DLC number, the channel is not paused by Sweeppea, and no step is held by Sweeppea support.

A drip that was edited after pausing, or that the platform blocked, is resumed in the app, where the full launch checklist runs. The drip picks up within 5 minutes.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. Because it changes data, the Send Message module and the Drip Marketing module must be enabled for your account.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `DripToken` | string | Yes | UUID v4 of a drip in status `paused`, not edited since it was paused. |
| `CalledFromMCP` | boolean | No | Set by the MCP server, ignore it. |
| `CalledFromCLI` | boolean | No | Set by the CLI, ignore it. |

## Request Example

```json
{
  "DripToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/messaging/resume-drip" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "DripToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/messaging/resume-drip', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    DripToken: "uuid-v4-string"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/messaging/resume-drip"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "DripToken": "uuid-v4-string"
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
    "APICalls": 312,
    "MaxAPICalls": 1500000
  },
  "Data": {
    "Drip": {
      "DripToken": "uuid-v4-string",
      "SweepstakesToken": "uuid-v4-string",
      "Name": "Welcome Series",
      "Description": "Three emails for new entrants",
      "Channel": "email",
      "Status": "active",
      "BlockedReason": null,
      "StaffHold": false,
      "Archived": false,
      "Trigger": "join",
      "StepsCount": 3,
      "Counters": {
        "Enrolled": 1240,
        "Active": 812,
        "Completed": 356,
        "Exited": {
          "Unsubscribed": 38,
          "Suppressed": 9,
          "Won": 4,
          "Removed": 2
        },
        "Deferred": {}
      },
      "Origin": "user",
      "ActivatedAt": "2026-09-20T14:02:11.000Z",
      "PausedAt": null,
      "CompletedAt": null,
      "CreationDate": "2026-09-18T10:15:40.000Z",
      "UpdatedAt": "2026-09-20T14:02:11.000Z"
    },
    "Note": "The drip picks up within the next few minutes (the sequencer runs every 5 minutes)."
  },
  "Message": "Drip campaign resumed successfully"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `Drip` | object | The drip after resuming. |
| `Note` | string | When sending restarts. |

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: DripToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "DripToken": "string (required) — UUID v4 of a drip in status paused, not changed since it was paused",
      "CalledFromMCP": "boolean (optional) — Set by the MCP server, ignore it",
      "CalledFromCLI": "boolean (optional) — Set by the CLI, ignore it"
    }
  }
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Drip not found. The DripToken must exist and belong to your account.",
  "Code": 404
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This drip cannot be resumed: its status is \"active\". Only a paused drip can be resumed.",
  "Code": 409,
  "Data": {
    "Status": "active"
  }
}
```

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This drip was changed after it was paused. Resume it from Drip Marketing in the app, where the full checklist runs.",
  "Code": 409,
  "Data": {
    "Status": "paused",
    "Reason": "EditedSincePause"
  }
}
```

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "Your plan does not include running drip campaigns. Contact support to activate them.",
  "Code": 403,
  "Data": {
    "Reason": "PlanDoesNotAllowSharing"
  }
}
```

**423 Locked**

```json
{
  "Response": false,
  "Message": "Sending on this channel is paused by Sweeppea right now. Try again later or contact support.",
  "Code": 423,
  "Data": {
    "Reason": "ChannelPaused"
  }
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

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "The Send Message module is not enabled for your account. Contact support to enable it.",
  "Code": 403
}
```

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "The Drip Marketing module is not enabled for your account. Contact support to enable it.",
  "Code": 403
}
```

**429 Too Many Requests**

```json
{
  "Response": false,
  "Message": "Rate limit exceeded. Your plan allows 60 API calls per minute. Wait a few seconds and try again.",
  "Code": 429
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

Every `400` carries a `Help.ExpectedBody` block listing every accepted parameter. A disabled or hibernating account and an exhausted monthly API quota answer `403`; the per-minute limit answers `429` — see [Concurrency & Rate Limiting](../concurrency.md).

## Notes

- `Data.Reason` on a refusal: `Blocked`, `EditedSincePause`, `StepsNotActivated` (409); `PlanDoesNotAllowSharing`, `ChannelNotInPlan`, `NoRegisteredSmsSender`, `StaffHold` (403); `ChannelPaused` (423).
- Contacts continue from the step they were waiting for; no step is sent twice.
- Recorded in your account log.
