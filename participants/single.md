# Fetch Single Participant

Retrieve complete data for a single participant in a sweepstakes, including all entries across collections.

## Endpoint

`POST /participants/single`

## Description

This endpoint allows you to fetch complete data for a single participant in a sweepstakes. The participant can be searched by ParticipantToken, KeyEmail, or KeyPhoneNumber. The endpoint searches across all collections (Participants, ParticipantsAmoe, OptOuts) and returns all entries if the participant appears multiple times.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter          | Type             | Required | Description                                    |
|--------------------|------------------|----------|------------------------------------------------|
| `SweepstakesToken` | String (UUID v4) | Yes      | Unique identifier for the sweepstakes          |
| `ParticipantToken` | String (UUID v4) | No*      | Unique identifier for the participant          |
| `KeyEmail`         | String           | No*      | Participant's email address                    |
| `KeyPhoneNumber`   | String           | No*      | Participant's phone number (10 digits)         |

*At least one search parameter (`ParticipantToken`, `KeyEmail`, or `KeyPhoneNumber`) is required.

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "KeyEmail": "participant@example.com"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/participants/single" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "uuid-v4-string",
    "KeyEmail": "participant@example.com"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/participants/single', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: 'uuid-v4-string',
    KeyEmail: 'participant@example.com'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/participants/single"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "KeyEmail": "participant@example.com"
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "SweepstakesToken": "uuid-v4-string",
    "SweepstakesName": "Summer Giveaway 2025",
    "SearchCriteria": {
      "KeyEmail": "participant@example.com"
    },
    "TotalEntries": 2,
    "Collections": ["Participants", "OptOuts"],
    "Entries": [
      {
        "ParticipantToken": "uuid-v4-string",
        "UserToken": "uuid-v4-string",
        "SweepstakesToken": "uuid-v4-string",
        "KeyEmail": "participant@example.com",
        "KeyPhoneNumber": "5551234567",
        "BonusEntries": 0,
        "CreationDate": "2025-06-15T10:30:00.000Z",
        "EntryPagesFields": {
          "FirstName": "John",
          "LastName": "Doe"
        },
        "Status": true,
        "Collection": "Participants"
      },
      {
        "ParticipantToken": "uuid-v4-string",
        "UserToken": "uuid-v4-string",
        "SweepstakesToken": "uuid-v4-string",
        "KeyEmail": "participant@example.com",
        "OptOutDate": "2025-07-01T14:20:00.000Z",
        "Collection": "OptOuts"
      }
    ]
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing Search Criteria: ParticipantToken, KeyEmail, or KeyPhoneNumber required",
  "Code": 400
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Participant Not Found in This Sweepstakes",
  "Code": 404
}
```

## Search Examples

**Search by ParticipantToken:**

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "ParticipantToken": "uuid-v4-string"
}
```

**Search by Phone Number:**

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "KeyPhoneNumber": "5551234567"
}
```
