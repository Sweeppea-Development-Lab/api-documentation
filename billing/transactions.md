# Fetch Billing Transactions

Retrieve all billing transactions for the authenticated user. Returns transaction history including invoices, payments, and billing details.

## Endpoint

`GET /billing/fetchtransactions`

## Description

This endpoint retrieves all billing transactions associated with the authenticated user. Transactions are returned in descending order by creation date, including payment status, invoice numbers, and transaction details.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Code Examples

### cURL

```bash
curl -X GET "https://api-v3.sweeppea.com/billing/fetchtransactions" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/billing/fetchtransactions', {
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

url = "https://api-v3.sweeppea.com/billing/fetchtransactions"
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
    "TotalTransactions": 8,
    "Transactions": [
      {
        "InvoiceNumber": "SP-789012",
        "TransactionID": "456789123456",
        "Description": "Monthly subscription payment",
        "Amount": 49.99,
        "AmountPrizes": 0,
        "Status": "paid",
        "ResponseCode": "1",
        "ErrorCode": "",
        "Views": 0,
        "IsFirstTransaction": false,
        "IsSandboxTransaction": false,
        "Refunded": false,
        "CronPay": false,
        "DocumentType": "invoice",
        "DataTransferTx": false,
        "VoidDate": null,
        "CreationDate": "2025-01-20T10:15:00.000Z",
        "ExpirationDate": "2025-02-20T10:15:00.000Z",
        "SentByEmail": [],
        "Downloads": []
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
