# Fetch Wallet Transactions

Retrieve all wallet transactions for the authenticated user. Returns transaction history including credits, debits, and transaction details.

## Endpoint

`GET /wallet/fetchtransactions`

## Description

This endpoint retrieves all wallet transactions associated with the authenticated user. Transactions are returned in descending order by creation date, including both credit and debit transactions with full details.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Code Examples

### cURL

```bash
curl -X GET "https://api-v3.sweeppea.com/wallet/fetchtransactions" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/wallet/fetchtransactions', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/wallet/fetchtransactions"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

response = requests.get(url, headers=headers)
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Data": {
    "TotalTransactions": 15,
    "Transactions": [
      {
        "TransactionID": "987654321012",
        "Description": "Payment from Entry Page",
        "CCType": "Visa",
        "CCLastFourDigits": "4532",
        "TransactionType": "credit",
        "Amount": "5.00",
        "ErrorCode": null,
        "Views": 0,
        "IsSandboxTransaction": false,
        "VoidedByAdmin": false,
        "Status": true,
        "CreationDate": "2025-01-15T14:30:00.000Z",
        "Notes": [],
        "Files": []
      }
    ]
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

**500 Internal Server Error**
```json
{
  "Response": false,
  "Message": "Internal Server Error",
  "Code": 500
}
```
