# Fetch Winners

Fetch all winners from a sweepstakes with pagination and search support.

## Endpoint

`POST winners/fetch`

## Description

This endpoint allows you to fetch all winners from your sweepstakes. You can paginate results and search for specific winners by email or phone number.

**Important Notes:**

- The sweepstakes must belong to the authenticated user
- Results are paginated with default 10 items per page
- Search works on email and phone number fields
- Winners are sorted by draw date (most recent first)
- Searches across **Participants**, **ParticipantsAmoe**, and **OptOuts** collections
- Each winner includes a **"BonusEntries"** field showing bonus entries count

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sweepstakesToken` | String | Required | The unique identifier of the sweepstakes |
| `page` | Number | Optional | Page number for pagination (default: 1) |
| `itemsPerPage` | Number | Optional | Number of items per page (default: 10) |
| `search` | String | Optional | Search term to filter by email or phone number |

## Request Example

```json
{
  "sweepstakesToken": "uuid-v4-string",
  "page": 1,
  "itemsPerPage": 10,
  "search": ""
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/winners/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "sweepstakesToken": "uuid-v4-string",
    "page": 1,
    "itemsPerPage": 10,
    "search": ""
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/winners/fetch', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    sweepstakesToken: "uuid-v4-string",
    page: 1,
    itemsPerPage: 10,
    search: ""
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/winners/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "sweepstakesToken": "uuid-v4-string",
    "page": 1,
    "itemsPerPage": 10,
    "search": ""
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Message": "2 winner(s) fetched successfully.",
  "TotalWinners": 2,
  "Winners": [
    {
      "ParticipantToken": "uuid-v4-string",
      "GroupToken": "uuid-v4-string",
      "GroupName": "Participants",
      "OptInDate": "2026-02-16T11:51:46.653Z",
      "KeyPhoneNumber": "1234567890",
      "KeyEmail": "john.doe@example.com",
      "EntryPagesFields": {
        "Email": "john.doe@example.com",
        "First_Name": "John",
        "Last_Name": "Doe",
        "Mobile_Number": "1234567890"
      },
      "BonusEntries": 0,
      "WinnerInfo": true,
      "WasNotify": false,
      "IsAmoe": false,
      "Handler": "sweepstakes-handler",
      "SweepsName": "My Sweepstakes",
      "DrawDateTime": "2026-02-16T12:28:40.000Z"
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

**400 Bad Request**
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
