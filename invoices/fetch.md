# Fetch Invoices

List the invoices of the authenticated account with the summary tiles — pending, paid and overdue totals.

## Endpoint

`POST /invoices/fetch`

## Description

This endpoint returns a paginated list of invoices together with the summary totals rendered above the Invoices dashboard. The summary always describes the **whole filtered set**, not just the page being returned: filtering by pending status and asking for page 3 still reports the total pending amount across every matching invoice. The `IsOverdue` flag is derived at read time and never stored — an invoice is overdue when it is still pending and its due date has passed.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `Status` | String | No | Filter by status: `draft`, `pending`, `paid` or `cancelled` |
| `FromDate` | String | No | ISO date (YYYY-MM-DD). Only invoices created on or after this day |
| `ToDate` | String | No | ISO date (YYYY-MM-DD). Only invoices created on or before this day |
| `Page` | Number | No | Page number (default: 1) |
| `Limit` | Number | No | Results per page (default: 50, maximum: 200) |

## Request Example

```json
{
  "Status": "pending",
  "FromDate": "2026-01-01",
  "Page": 1,
  "Limit": 25
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/invoices/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "Status": "pending",
        "FromDate": "2026-01-01",
        "Page": 1,
        "Limit": 25
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/invoices/fetch', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    Status: "pending",
    FromDate: "2026-01-01",
    Page: 1,
    Limit: 25
})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/invoices/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "Status": "pending",
    "FromDate": "2026-01-01",
    "Page": 1,
    "Limit": 25
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 149,
    "MaxAPICalls": 1500000
  },
  "Data": {
    "Invoices": [
      {
        "InvoiceToken": "uuid-v4-string",
        "PublicToken": "uuid-v4-string",
        "InvoiceNumber": "INV-2026-00042",
        "Title": "August retainer",
        "CreationDate": "2026-08-01T14:02:11.000Z",
        "IssueDate": "2026-08-01T00:00:00.000Z",
        "DueDate": "2026-08-16T00:00:00.000Z",
        "RecipientName": "Acme Corporation",
        "RecipientEmail": "billing@acme.example",
        "Currency": "USD",
        "Subtotal": 4500,
        "DiscountAmount": 0,
        "TaxEnabled": true,
        "TaxAmount": 382.5,
        "Total": 4882.5,
        "Status": "pending",
        "PaymentMethod": "card",
        "IsManualPayment": false,
        "IsOverdue": false,
        "DaysElapsed": 3,
        "DaysOverdue": 0,
        "Views": 2,
        "Notifications": 1,
        "LastViewDate": "2026-08-03T09:41:00.000Z",
        "LastNotificationDate": "2026-08-01T14:05:00.000Z",
        "PaidDate": null,
        "Archived": false,
        "ProcessingFeePercentage": 3,
        "MerchantFeePercentage": 3
      }
    ],
    "Summary": {
      "TotalPending": 12480.75,
      "TotalPaid": 38210,
      "CountPending": 4,
      "CountPaid": 11,
      "CountDraft": 2,
      "CountOverdue": 1,
      "CountTotal": 17
    },
    "Pagination": {
      "Page": 1,
      "Limit": 25,
      "TotalInvoices": 17,
      "TotalPages": 1
    }
  },
  "Message": "(OK) Invoices fetched successfully."
}
```

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid Status. Accepted values: draft, pending, paid, cancelled",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "Status": "string (optional) \u2014 Filter by status: draft | pending | paid | cancelled"
    }
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid FromDate. Use an ISO date such as 2026-01-31.",
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

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal Server Error",
  "Code": 500
}
```

## Notes

- **🔒 Module Access:** The Invoices module is disabled by default. An administrator must enable it for your account before any of these endpoints will respond
- **📊 Summary Scope:** The summary totals cover the entire filtered set, never just the current page
- **⏰ Overdue Is Derived:** `IsOverdue` is computed at read time from the due date — the stored status stays `pending`
- **🗂️ Archived Included:** Archived invoices are returned with an `Archived: true` flag so you can filter them client-side
- **📄 Full Document:** Line items, notes, terms, the event timeline and the QR code come from `/invoices/single`
