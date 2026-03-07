# Fetch Consumptions

Retrieve monthly and yearly consumption totals for the authenticated user. Returns aggregated billing data from all transactions and data transfers.

## Endpoint

`GET /billing/consumptions`

## Description

This endpoint retrieves all historical consumption data for the authenticated user, aggregated by year and month. It calculates the total amount charged from both billing transactions and data transfer logs, providing a comprehensive view of account spending over time.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Code Examples

### cURL

```bash
curl -X GET "https://api-v3.sweeppea.com/billing/consumptions" \
  -H "Authorization: Bearer uuid-v4-string" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/billing/consumptions', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer uuid-v4-string',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/billing/consumptions"
headers = {
    "Authorization": "Bearer uuid-v4-string",
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
    "GrandTotal": 115776.91,
    "Consumptions": [
      {
        "Year": 2026,
        "Total": 813.69,
        "Months": [
          {
            "Month": 1,
            "Total": 813.69
          }
        ]
      },
      {
        "Year": 2025,
        "Total": 47585.10,
        "Months": [
          {
            "Month": 1,
            "Total": 2543.00
          },
          {
            "Month": 2,
            "Total": 599.00
          }
        ]
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

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `GrandTotal` | Number | Total amount charged across all years and months |
| `Consumptions` | Array | Array of yearly consumption data |
| `Year` | Number | Calendar year |
| `Total` | Number | Total amount for the year or month |
| `Months` | Array | Array of monthly consumption data within the year |
| `Month` | Number | Month number (1-12) |
