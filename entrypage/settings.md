# Fetch Entry Page Settings

Retrieve the entry page settings for a given sweepstakes. Validates that the entry page belongs to both the sweepstakes and the authenticated user.

## Endpoint

`POST /entrypage/settings`

## Description

This endpoint retrieves the entry page settings associated with the given `SweepstakesToken`. Ownership is enforced — the entry page must belong to both the specified sweepstakes and the authenticated user. Returns `EntryPageToken`, `SweepstakesToken`, `CreationDate`, `EntryPageTitle`, and the full `Settings` object.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `SweepstakesToken` | String | Yes | Unique identifier of the sweepstakes (UUID v4) |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/entrypage/settings" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"SweepstakesToken":"uuid-v4-string"}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/entrypage/settings', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: 'uuid-v4-string'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/entrypage/settings"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string"
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "EntryPageToken": "uuid-v4-string",
    "SweepstakesToken": "uuid-v4-string",
    "EntryPageTitle": "My Entry Page",
    "Settings": {
      "SweepstakesHandler": "MYHANDLER",
      "EntryPageType": 2,
      "EntryPageHeadline": "Welcome!",
      "EntryPageDescription": "Complete the entry form to participate.",
      "EmailOptInSwitch": false,
      "SMSTextOptInSwitch": false,
      "BonusEntriesSwitch": false,
      "TermsConditionsSwitch": false,
      "ReCaptcha": true,
      "GeoLocation": true,
      "CollectStatistics": true,
      "...": "..."
    },
    "CreationDate": "2026-02-19T16:03:16.917Z"
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "SweepstakesToken is Required",
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
  "Message": "Entry Page Not Found",
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

- **Ownership Verification:** The entry page must belong to both the `SweepstakesToken` and the authenticated user.
- **Sweepstakes Identification:** Sweepstakes are identified by their unique SweepstakesToken (UUID v4).
- **Full Settings:** The `Settings` object contains all entry page configuration including design, security, webhooks, social, bonus entries, and more.
- **Not Found:** Returns 404 if the entry page doesn't exist or belongs to another user or sweepstakes.
