# Fetch Data Transfer

Retrieve all data transfer logs for a specific sweepstakes. Returns bandwidth usage history and payment details.

## Endpoint

`POST /billing/datatransfer`

## Description

This endpoint retrieves all data transfer logs for a specific sweepstakes. The sweepstakes must exist and belong to the authenticated user. Returns data transferred in bytes, payment status, and transaction details.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `SweepstakesToken` | string | Yes | UUID v4 token of the sweepstakes |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/billing/datatransfer" \
  -H "Authorization: Bearer uuid-v4-string" \
  -H "Content-Type: application/json" \
  -d '{"SweepstakesToken":"uuid-v4-string"}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/billing/datatransfer', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer uuid-v4-string',
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

url = "https://api-v3.sweeppea.com/billing/datatransfer"
headers = {
    "Authorization": "Bearer uuid-v4-string",
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
    "TotalRecords": 3,
    "DataTransfer": [
      {
        "SweepstakesToken": "uuid-v4-string",
        "DataTransferred": 125000000,
        "Paid": true,
        "AmountPaid": 15.50,
        "Rate": 1,
        "TransactionID": "numeric-string",
        "Date": "2025-01-20T10:15:00.000Z"
      }
    ]
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing SweepstakesToken in request body",
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
  "Message": "Sweepstakes not found or does not belong to user",
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
