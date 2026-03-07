# Delete Participant

Delete a participant from a sweepstakes.

## Endpoint

`DELETE /participants/delete`

## Description

This endpoint allows you to delete a participant from a sweepstakes. The participant is identified by ParticipantToken and will be removed from whichever collection it exists in (Participants, ParticipantsAmoe, or OptOuts).

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter          | Type             | Required | Description                                 |
|--------------------|------------------|----------|---------------------------------------------|
| `SweepstakesToken` | String (UUID v4) | Yes      | Unique identifier for the sweepstakes       |
| `ParticipantToken` | String (UUID v4) | Yes      | Unique identifier for the participant       |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "ParticipantToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X DELETE "https://api-v3.sweeppea.com/participants/delete" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "uuid-v4-string",
    "ParticipantToken": "uuid-v4-string"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/participants/delete', {
  method: 'DELETE',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: 'uuid-v4-string',
    ParticipantToken: 'uuid-v4-string'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/participants/delete"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "ParticipantToken": "uuid-v4-string"
}

response = requests.delete(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "Participant Deleted Successfully",
  "Data": {
    "ParticipantToken": "uuid-v4-string",
    "SweepstakesToken": "uuid-v4-string",
    "DeletedFrom": ["Participants"],
    "DeletedCount": 1
  }
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
