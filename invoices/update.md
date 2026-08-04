# Update Invoice

Edit, publish or cancel an invoice. Publishing and cancelling are status transitions, not separate endpoints.

## Endpoint

`POST /invoices/update`

## Description

This endpoint edits an invoice while it is still `draft` or `pending`, and is also how an invoice is **published** (`Status: "pending"`) or **cancelled** (`Status: "cancelled"`). Every field is optional: anything omitted keeps its stored value, and `Items` and `BillTo` are replaced wholesale rather than merged. Publishing does **not** email the recipient.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header. The account must also have the **Invoices module enabled** — it is disabled by default and granted by an administrator.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `InvoiceToken` | String | Yes | UUID v4 of the invoice to edit |
| `BillTo` | Object | No | Replaces the whole recipient. Still requires `Name` and a valid `Email` |
| `Items` | Array | No | Replaces the whole line item set. Maximum 60 items |
| `Title` | String | No | Invoice title / concept (max 200 characters) |
| `IssueDate` | String | No | ISO date (YYYY-MM-DD) |
| `DueDate` | String | No | ISO date (YYYY-MM-DD) |
| `DiscountAmount` | Number | No | Flat discount, clamped to the subtotal |
| `TaxEnabled` | Boolean | No | Apply tax |
| `TaxLabel` | String | No | Tax label |
| `TaxRate` | Number | No | Tax percentage from 0 to 100 |
| `PaymentMethod` | String | No | `card`, `check` or `transfer` |
| `PaymentInstructions` | String | No | Offline instructions. Forced empty when the method is `card` |
| `NotesToRecipient` | String | No | Notes shown to the recipient (max 3000 characters) |
| `Terms` | String | No | Terms and conditions (max 3000 characters) |
| `Status` | String | No | `pending` publishes a draft, `cancelled` cancels the invoice |

## Request Example

```json
{
  "InvoiceToken": "uuid-v4-string",
  "Title": "August retainer (revised)",
  "Items": [
    {
      "Description": "Consulting",
      "Quantity": 12,
      "Rate": 450
    }
  ],
  "Status": "pending"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/invoices/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "InvoiceToken": "uuid-v4-string",
        "Title": "August retainer (revised)",
        "Items": [
            {
                "Description": "Consulting",
                "Quantity": 12,
                "Rate": 450
            }
        ],
        "Status": "pending"
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/invoices/update', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    InvoiceToken: "uuid-v4-string",
    Title: "August retainer (revised)",
    Items: [
        {
            "Description": "Consulting",
            "Quantity": 12,
            "Rate": 450
        }
    ],
    Status: "pending"
})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/invoices/update"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "InvoiceToken": "uuid-v4-string",
    "Title": "August retainer (revised)",
    "Items": [
        {
            "Description": "Consulting",
            "Quantity": 12,
            "Rate": 450
        }
    ],
    "Status": "pending"
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
    "Action": "issued",
    "Status": "pending",
    "PreviousStatus": "draft",
    "Items": [
      {
        "Id": "",
        "Description": "Consulting",
        "Quantity": 12,
        "Rate": 450,
        "Amount": 5400
      }
    ],
    "Subtotal": 5400,
    "DiscountAmount": 100,
    "TaxEnabled": true,
    "TaxRate": 8.5,
    "TaxAmount": 450.5,
    "Total": 5750.5,
    "PaymentMethod": "card",
    "PublicLink": "https://app.sweeppea.com/invoice?t=uuid-v4-string",
    "Changes": [
      {
        "Field": "Title",
        "From": "August retainer",
        "To": "August retainer (revised)"
      },
      {
        "Field": "Status",
        "From": "draft",
        "To": "pending"
      }
    ]
  },
  "Message": "Invoice issued successfully."
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
      "InvoiceToken": "string (required) \u2014 UUID v4 of the invoice to edit",
      "Status": "string (optional) \u2014 \"pending\" publishes a draft, \"cancelled\" cancels the invoice. Never demotes back to draft"
    }
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "An invoice can never be moved back to draft. Accepted transitions: pending (publish) or cancelled.",
  "Code": 400
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid Status. Accepted transitions: pending or cancelled.",
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

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "The Invoices module is not enabled for this account. Contact support to request access.",
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

**409 Conflict**

```json
{
  "Response": false,
  "Message": "This invoice is paid and can no longer be modified. Only draft and pending invoices are editable.",
  "Code": 409
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
- **🚦 Status Machine:** `draft → pending → paid`, with `cancelled` reachable from `draft` and `pending`. Payment itself happens on the public page or through an administrator, never through this endpoint
- **⛔ Never Demoted:** `Status: "draft"` is always rejected. An invoice only moves forward
- **🔒 Immutable When Closed:** `paid` and `cancelled` invoices return `409` — they are financial history
- **🧊 Frozen Fees:** The commission percentages are never recomputed on update
- **📧 No Email:** Publishing does not notify the recipient. Distribute `PublicLink` yourself
- **📋 Audit Trail:** Every call appends an entry to the invoice's modifications log with the exact field-level changes returned in `Changes`
