# Single Invoice

Retrieve one invoice in full — line items, totals, public payment link, QR code, statistics and event timeline.

## Endpoint

`POST /invoices/single`

## Description

This endpoint returns everything about a single invoice in one call: the recipient, every line item, the computed totals, payment details, the public payment link, a ready-to-use QR code, view statistics, the event timeline and the modifications log. It intentionally replaces what would otherwise be three separate round trips.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `InvoiceToken` | String | Yes | UUID v4 of the invoice to read |
| `IncludeEvents` | Boolean | No | Include the event timeline (default: true) |
| `IncludeQrCode` | Boolean | No | Include the QR code as a data URL (default: true) |

## Request Example

```json
{
  "InvoiceToken": "uuid-v4-string",
  "IncludeEvents": true,
  "IncludeQrCode": true
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/invoices/single" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "InvoiceToken": "uuid-v4-string",
        "IncludeEvents": true,
        "IncludeQrCode": true
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/invoices/single', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    InvoiceToken: "uuid-v4-string",
    IncludeEvents: true,
    IncludeQrCode: true
})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/invoices/single"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "InvoiceToken": "uuid-v4-string",
    "IncludeEvents": True,
    "IncludeQrCode": True
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
    "Invoice": {
      "InvoiceToken": "uuid-v4-string",
      "PublicToken": "uuid-v4-string",
      "InvoiceNumber": "INV-2026-00042",
      "Title": "August retainer",
      "CreationDate": "2026-08-01T14:02:11.000Z",
      "UpdatedAt": "2026-08-02T10:00:00.000Z",
      "IssueDate": "2026-08-01T00:00:00.000Z",
      "DueDate": "2026-08-16T00:00:00.000Z",
      "Currency": "USD",
      "BillTo": {
        "Name": "Acme Corporation",
        "Email": "billing@acme.example",
        "Phone": "3055551234",
        "Company": "Acme Corp",
        "Address": "1 Main St",
        "City": "Miami",
        "State": "Florida",
        "ZipCode": "33101",
        "Country": "United States",
        "TaxId": "12-3456789"
      },
      "Items": [
        {
          "Id": "",
          "Description": "Consulting",
          "Quantity": 10,
          "Rate": 450,
          "Amount": 4500
        }
      ],
      "Subtotal": 4500,
      "DiscountAmount": 0,
      "TaxEnabled": true,
      "TaxLabel": "Sales Tax",
      "TaxRate": 8.5,
      "TaxAmount": 382.5,
      "Total": 4882.5,
      "Status": "pending",
      "PaymentMethod": "card",
      "PaymentInstructions": null,
      "NotesToRecipient": "Thank you for your business.",
      "Terms": "Net 15.",
      "ProcessingFeePercentage": 3,
      "MerchantFeePercentage": 3,
      "IsSandboxInvoice": false,
      "Archived": false
    },
    "Payment": {
      "IsPaid": false,
      "IsManualPayment": false,
      "PaidDate": null,
      "Method": null,
      "PayerName": null,
      "PayerEmail": null,
      "CCType": null,
      "CCLastFourDigits": null,
      "GrossAmount": null,
      "ProcessingFee": null,
      "NetAmount": null,
      "Reference": null
    },
    "PublicLink": "https://app.sweeppea.com/invoice?t=uuid-v4-string",
    "QrCode": "data:image/png;base64,...",
    "Stats": {
      "Views": 2,
      "UniqueViews": 1,
      "FirstViewDate": "2026-08-02T09:00:00.000Z",
      "LastViewDate": "2026-08-03T09:41:00.000Z",
      "Notifications": 1,
      "LastNotificationDate": "2026-08-01T14:05:00.000Z",
      "PaymentAttempts": 0,
      "FailedAttempts": 0
    },
    "Derived": {
      "IsOverdue": false,
      "DaysSinceIssued": 3,
      "DaysOverdue": 0,
      "DaysToPayment": null
    },
    "Events": {
      "Totals": {
        "created": 1,
        "viewed": 2,
        "notified": 1
      },
      "Timeline": [
        {
          "EventType": "viewed",
          "Description": "Invoice viewed by the recipient",
          "CreationDate": "2026-08-03T09:41:00.000Z",
          "IpAddress": "203.0.113.10",
          "Browser": "Chrome",
          "DeviceType": "desktop",
          "OperatingSystem": "macOS",
          "Referrer": ""
        }
      ]
    },
    "ModificationsLog": []
  },
  "Message": "(OK) Invoice fetched successfully."
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
      "InvoiceToken": "string (required) \u2014 UUID v4 of the invoice to read",
      "IncludeEvents": "boolean (optional) \u2014 Include the event timeline, defaults to true",
      "IncludeQrCode": "boolean (optional) \u2014 Include the QR code as a data URL, defaults to true"
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
- **🔗 Public Link:** `PublicLink` is the payment page you hand to the recipient. This API never emails it for you
- **📱 QR Code:** Returned as a PNG data URL. If rendering fails the field comes back `null` and the rest of the payload is unaffected
- **🪶 Trim The Payload:** Set `IncludeEvents` or `IncludeQrCode` to `false` when you do not need them
- **🧾 Timeline Cap:** The event timeline returns the 200 newest events
- **🔐 Ownership:** An invoice belonging to another account returns `404`, never its contents
