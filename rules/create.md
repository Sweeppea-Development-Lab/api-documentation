# Create Rules

Create official rules for a specific sweepstakes. The first rule created will be marked as primary, subsequent rules will be secondary.

## Endpoint

`POST /rules/create`

## Description

This endpoint creates a new official rule document for a sweepstakes. The sweepstakes must exist and belong to the authenticated user. If no rules exist for the sweepstakes, the new rule will be marked as primary. Otherwise, it will be marked as secondary.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Max Length | Description |
|-----------|------|----------|------------|-------------|
| `SweepstakesToken` | String (UUID v4) | Yes | 36 | The unique identifier of the sweepstakes |
| `Title` | String | Yes | 100 | The title of the official rules |
| `DocumentContent` | String | Yes | 1,000,000 | The full HTML content of the official rules |

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/rules/create" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "YOUR_SWEEPSTAKES_TOKEN",
    "Title": "Official Rules",
    "DocumentContent": "<p>Full HTML content of official rules...</p>"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/rules/create', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: 'YOUR_SWEEPSTAKES_TOKEN',
    Title: 'Official Rules',
    DocumentContent: '<p>Full HTML content of official rules...</p>'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/rules/create"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "YOUR_SWEEPSTAKES_TOKEN",
    "Title": "Official Rules",
    "DocumentContent": "<p>Full HTML content of official rules...</p>"
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
    "RulesToken": "bc225a85-c16f-4339-a1d6-d0c6d62fa18d",
    "SweepstakesToken": "1770a839-abeb-42b4-b003-888073ba1e9b",
    "Title": "Test Rule Primary",
    "Primary": true,
    "CreationDate": "2026-01-23T11:30:22.979Z"
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

```json
{
  "Response": false,
  "Message": "Title is Required",
  "Code": 400
}
```

```json
{
  "Response": false,
  "Message": "DocumentContent is Required",
  "Code": 400
}
```

```json
{
  "Response": false,
  "Message": "Title exceeds maximum length of 100 characters",
  "Code": 400
}
```

```json
{
  "Response": false,
  "Message": "DocumentContent exceeds maximum length of 1000000 characters",
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
  "Message": "Sweepstakes Not Found",
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
