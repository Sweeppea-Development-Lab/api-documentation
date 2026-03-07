# Draw Winners

Draw winners from your sweepstakes using a weighted random selection algorithm.

## Endpoint

`POST winners/draw`

## Description

This endpoint allows you to draw winners from your sweepstakes using a weighted random selection algorithm. The algorithm ensures fair distribution while giving participants with more bonus entries a proportionally higher chance of winning.

**Important Notes:**

- The sweepstakes must belong to the authenticated user
- The sweepstakes cannot be paused or archived
- At least one participant must be available who hasn't won yet
- Winners are marked with the sweepstakes handler automatically

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sweepstakesToken` | String | Required | The unique identifier of the sweepstakes |
| `howManyWinnersToPick` | Number | Required | Number of winners to select |
| `group` | String | Required | Group token or `"allgroups"` to include all participants |
| `completedEntries` | Boolean | Optional | Filter to only completed entries |
| `includeOptedOutParticipants` | Boolean | Optional | Include opted-out participants in the drawing |
| `doNotIncludeSpamParticipants` | Boolean | Optional | Exclude participants flagged as spam |

## Request Example

```json
{
  "sweepstakesToken": "uuid-v4-string",
  "howManyWinnersToPick": 1,
  "group": "allgroups",
  "completedEntries": false,
  "includeOptedOutParticipants": false,
  "doNotIncludeSpamParticipants": true
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/winners/draw" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "sweepstakesToken": "uuid-v4-string",
    "howManyWinnersToPick": 1,
    "group": "allgroups",
    "completedEntries": false,
    "includeOptedOutParticipants": false,
    "doNotIncludeSpamParticipants": true
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/winners/draw', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    sweepstakesToken: "uuid-v4-string",
    howManyWinnersToPick: 1,
    group: "allgroups",
    completedEntries: false,
    includeOptedOutParticipants: false,
    doNotIncludeSpamParticipants: true
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/winners/draw"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "sweepstakesToken": "uuid-v4-string",
    "howManyWinnersToPick": 1,
    "group": "allgroups",
    "completedEntries": False,
    "includeOptedOutParticipants": False,
    "doNotIncludeSpamParticipants": True
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Message": "1 winner(s) were selected successfully.",
  "Winners": [
    {
      "ParticipantToken": "uuid-v4-string",
      "FirstName": "John",
      "LastName": "Doe",
      "Email": "john.doe@example.com",
      "PhoneNumber": "1234567890"
    }
  ]
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

**400 Bad Request - Invalid Parameters**
```json
{
  "Response": false,
  "Message": "Invalid parameters in body object, read documentation.",
  "Code": 400
}
```

**404 Not Found**
```json
{
  "Response": false,
  "Message": "Sweepstakes not found.",
  "Code": 404
}
```

**403 Forbidden - No Permission**
```json
{
  "Response": false,
  "Message": "You do not have permission to access this sweepstakes.",
  "Code": 403
}
```

**400 Bad Request - Paused**
```json
{
  "Response": false,
  "Message": "Cannot draw winners from a paused sweepstakes.",
  "Code": 400
}
```

**400 Bad Request - Archived**
```json
{
  "Response": false,
  "Message": "Cannot draw winners from an archived sweepstakes.",
  "Code": 400
}
```

**400 Bad Request - No Participants**
```json
{
  "Response": false,
  "Message": "No participants available to draw winners.",
  "Code": 400
}
```
