# Fetch Participants

Retrieve a paginated list of participants from a sweepstakes, with optional search and date filtering.

## Endpoint

`POST /participants/fetch`

## Description

This endpoint allows you to fetch a paginated list of participants from a sweepstakes. Results are returned 20 per page, sorted by creation date (most recent first). The endpoint supports searching by first name, last name, email, or phone number across all collections (Participants, ParticipantsAmoe, OptOuts).

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter          | Type              | Required | Description                                                                               |
|--------------------|-------------------|----------|-------------------------------------------------------------------------------------------|
| `sweepstakesToken` | String (UUID v4)  | Yes      | Unique identifier for the sweepstakes                                                     |
| `page`             | Number            | No       | Page number for pagination (default: 1)                                                   |
| `search`           | String            | No       | Search term to filter by first name, last name, email, or phone number (case-insensitive) |
| `optInDate`        | Date (YYYY-MM-DD) | No       | Filter participants by specific opt-in date                                               |
| `startDate`        | Date (YYYY-MM-DD) | No       | Filter participants from this date (requires `endDate`)                                   |
| `endDate`          | Date (YYYY-MM-DD) | No       | Filter participants until this date (requires `startDate`)                                |

## Request Example

```json
{
  "sweepstakesToken": "uuid-v4-string",
  "page": 1,
  "search": "john",
  "startDate": "2025-01-01",
  "endDate": "2025-01-31"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/participants/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "sweepstakesToken": "uuid-v4-string",
    "page": 1,
    "search": "john",
    "startDate": "2025-01-01",
    "endDate": "2025-01-31"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/participants/fetch', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    sweepstakesToken: 'uuid-v4-string',
    page: 1,
    search: 'john',
    startDate: '2025-01-01',
    endDate: '2025-01-31'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/participants/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "sweepstakesToken": "uuid-v4-string",
    "page": 1,
    "search": "john",
    "startDate": "2025-01-01",
    "endDate": "2025-01-31"
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Participants": [
    {
      "ParticipantToken": "uuid-v4-string",
      "UserToken": "uuid-v4-string",
      "SweepstakesToken": "uuid-v4-string",
      "KeyEmail": "john.doe@example.com",
      "KeyPhoneNumber": "5551234567",
      "FirstName": "John",
      "LastName": "Doe",
      "BonusEntries": 0,
      "CreationDate": "2025-06-15T10:30:00.000Z",
      "Status": true,
      "Collection": "Participants"
    },
    {
      "ParticipantToken": "uuid-v4-string",
      "UserToken": "uuid-v4-string",
      "SweepstakesToken": "uuid-v4-string",
      "KeyEmail": "jane.doe@example.com",
      "KeyPhoneNumber": "5559876543",
      "FirstName": "Jane",
      "LastName": "Doe",
      "CreationDate": "2025-06-14T15:20:00.000Z",
      "Status": true,
      "Collection": "Participants"
    }
  ],
  "Pagination": {
    "Page": 1,
    "Limit": 20,
    "Total": 45,
    "TotalPages": 3
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid or Missing sweepstakesToken",
  "Code": 400
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Sweepstakes Not Found or Access Denied",
  "Code": 404
}
```

## Request Examples by Use Case

**Fetch Without Search**

```json
{
  "sweepstakesToken": "uuid-v4-string",
  "page": 1
}
```

**Fetch With Search and Date Range**

```json
{
  "sweepstakesToken": "uuid-v4-string",
  "page": 1,
  "search": "john",
  "startDate": "2025-01-01",
  "endDate": "2025-01-31"
}
```

**Fetch By Specific Opt-In Date**

```json
{
  "sweepstakesToken": "uuid-v4-string",
  "page": 1,
  "optInDate": "2025-01-15"
}
```

## Notes

**Pagination & Results:**
- Returns 20 participants per page.
- Results are sorted by `CreationDate` (most recent first).
- Each participant includes a `"Collection"` field indicating its source.
- Each participant includes a `"BonusEntries"` field showing the bonus entries count.

**Search Capabilities:**
- Searches across `Participants`, `ParticipantsAmoe`, and `OptOuts` collections.
- Search is case-insensitive and matches partial strings.
- Search applies to: `FirstName`, `LastName`, `KeyEmail`, and `KeyPhoneNumber`.
- Filter by `optInDate` (specific date) uses the `CreationDate` field.
- Filter by date range (`startDate` and `endDate`) uses the `CreationDate` field.

**Access Control:**
- Participants must belong to the specified sweepstakes.
- Sweepstakes must belong to the authenticated user.
