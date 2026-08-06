# Delete Invoice

Permanently delete an invoice and its event trail. Wallet transactions and commissions are kept.

## Endpoint

`POST /invoices/delete`

## Description

This endpoint permanently deletes an invoice and every event recorded against it. The wallet transaction and the Sweeppea commission of a **paid** invoice are deliberately left untouched: that money actually moved and belongs to the financial record. Any status can be deleted, including `paid`, and the deletion is irreversible.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `InvoiceToken` | String | Yes | UUID v4 of the invoice to delete |

## Request Example

```json
{
  "InvoiceToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/invoices/delete" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "InvoiceToken": "uuid-v4-string"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/invoices/delete', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    InvoiceToken: "uuid-v4-string"
})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/invoices/delete"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "InvoiceToken": "uuid-v4-string"
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
    "InvoiceToken": "uuid-v4-string",
    "InvoiceNumber": "INV-2026-00001",
    "PreviousStatus": "cancelled",
    "InvoicesDeleted": 1,
    "EventsDeleted": 5,
    "FinancialRecords": "The wallet transaction and the Sweeppea commission of a paid invoice are kept on purpose."
  },
  "Message": "Invoice deleted successfully."
}
```

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Missing required parameter: InvoiceToken",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "InvoiceToken": "string (required) \u2014 UUID v4 of the invoice to delete"
    }
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid InvoiceToken. It must be a valid UUID v4 string.",
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
  "Message": "Invoice not found.",
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

## Notes

- **🔒 Module Access:** The Invoices module is disabled by default. An administrator must enable it for your account before any of these endpoints will respond
- **♻️ Irreversible:** The invoice document and every one of its events are removed permanently
- **💰 Money Survives:** Wallet transactions, Sweeppea commissions and consolidated billing rows are never deleted
- **🚫 Not The Same As Cancelling:** To close an invoice while keeping its history, use `/invoices/update` with `Status: "cancelled"`
- **🔁 No Bulk Delete:** Loop this endpoint to remove several invoices
- **🔐 Ownership:** An invoice belonging to another account returns `404` and is never touched
