# Create Invoice

Create an invoice from a recipient and a set of line items. Every amount is recomputed server-side.

## Endpoint

`POST /invoices/create`

## Description

This endpoint creates an invoice in `draft` status unless `Status: "pending"` is sent. Every monetary value is **recomputed server-side** — any `Subtotal`, `Total` or per-item `Amount` in the request body is ignored. The commission percentages are frozen from the account plan at creation time so a later plan change never rewrites the economics of an already issued document. Creating an invoice does **not** email the recipient.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `BillTo` | Object | Yes | Recipient. `Name` and a valid `Email` are mandatory |
| `Items` | Array | Yes | At least one entry with a `Description`. Maximum 60 items |
| `Title` | String | No | Invoice title / concept (max 200 characters) |
| `IssueDate` | String | No | ISO date (YYYY-MM-DD), defaults to today |
| `DueDate` | String | No | ISO date, defaults to today plus the account's default due days |
| `DiscountAmount` | Number | No | Flat discount, clamped to the subtotal |
| `TaxEnabled` | Boolean | No | Apply tax (default: false) |
| `TaxLabel` | String | No | Tax label (default: "Tax") |
| `TaxRate` | Number | No | Tax percentage from 0 to 100 |
| `PaymentMethod` | String | No | `card` (default), `check`, `transfer` or `recipient` (the payer chooses on the invoice page) |
| `PaymentInstructions` | String | No | Offline instructions. Only stored for `check` / `transfer` |
| `NotesToRecipient` | String | No | Notes shown to the recipient (max 3000 characters) |
| `Terms` | String | No | Terms and conditions (max 3000 characters) |
| `Status` | String | No | `draft` (default) or `pending` to publish immediately |

## Request Example

```json
{
  "BillTo": {
    "Name": "Acme Corporation",
    "Email": "billing@acme.example",
    "Company": "Acme Corp",
    "City": "Miami",
    "State": "Florida",
    "Country": "United States"
  },
  "Title": "August retainer",
  "Items": [
    {
      "Description": "Consulting",
      "Quantity": 10,
      "Rate": 450
    },
    {
      "Description": "Setup fee",
      "Quantity": 1,
      "Rate": 250
    }
  ],
  "DiscountAmount": 100,
  "TaxEnabled": true,
  "TaxLabel": "Sales Tax",
  "TaxRate": 8.5
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/invoices/create" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "BillTo": {
            "Name": "Acme Corporation",
            "Email": "billing@acme.example",
            "Company": "Acme Corp",
            "City": "Miami",
            "State": "Florida",
            "Country": "United States"
        },
        "Title": "August retainer",
        "Items": [
            {
                "Description": "Consulting",
                "Quantity": 10,
                "Rate": 450
            },
            {
                "Description": "Setup fee",
                "Quantity": 1,
                "Rate": 250
            }
        ],
        "DiscountAmount": 100,
        "TaxEnabled": true,
        "TaxLabel": "Sales Tax",
        "TaxRate": 8.5
    }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/invoices/create', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    BillTo: {
        "Name": "Acme Corporation",
        "Email": "billing@acme.example",
        "Company": "Acme Corp",
        "City": "Miami",
        "State": "Florida",
        "Country": "United States"
    },
    Title: "August retainer",
    Items: [
        {
            "Description": "Consulting",
            "Quantity": 10,
            "Rate": 450
        },
        {
            "Description": "Setup fee",
            "Quantity": 1,
            "Rate": 250
        }
    ],
    DiscountAmount: 100,
    TaxEnabled: true,
    TaxLabel: "Sales Tax",
    TaxRate: 8.5
})
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/invoices/create"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "BillTo": {
        "Name": "Acme Corporation",
        "Email": "billing@acme.example",
        "Company": "Acme Corp",
        "City": "Miami",
        "State": "Florida",
        "Country": "United States"
    },
    "Title": "August retainer",
    "Items": [
        {
            "Description": "Consulting",
            "Quantity": 10,
            "Rate": 450
        },
        {
            "Description": "Setup fee",
            "Quantity": 1,
            "Rate": 250
        }
    ],
    "DiscountAmount": 100,
    "TaxEnabled": True,
    "TaxLabel": "Sales Tax",
    "TaxRate": 8.5
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**201 Created**

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
    "PublicToken": "uuid-v4-string",
    "InvoiceNumber": "INV-2026-00001",
    "Status": "draft",
    "IssueDate": "2026-08-04T18:22:22.737Z",
    "DueDate": "2026-08-19T18:22:22.737Z",
    "Currency": "USD",
    "Items": [
      {
        "Id": "",
        "Description": "Consulting",
        "Quantity": 10,
        "Rate": 450,
        "Amount": 4500
      },
      {
        "Id": "",
        "Description": "Setup fee",
        "Quantity": 1,
        "Rate": 250,
        "Amount": 250
      }
    ],
    "Subtotal": 4750,
    "DiscountAmount": 100,
    "TaxEnabled": true,
    "TaxRate": 8.5,
    "TaxAmount": 395.25,
    "AmountBeforeFees": 5045.25,
    "SweeppeaFeeAmount": 161.02,
    "MerchantFeeAmount": 161.02,
    "FeeAmount": 322.04,
    "FeesChargedToRecipient": true,
    "Total": 5367.29,
    "PaymentMethod": "card",
    "ProcessingFeePercentage": 3,
    "MerchantFeePercentage": 3,
    "PublicLink": "https://app.sweeppea.com/invoice?t=uuid-v4-string"
  },
  "Message": "Invoice Created Successfully"
}
```

## Error Responses

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "BillTo requires at least a Name and a valid Email address.",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "BillTo": "object (required) \u2014 { Name (required), Email (required), Phone, Company, Address, City, State, ZipCode, Country, TaxId }",
      "Items": "array (required) \u2014 [{ Description (required), Quantity, Rate }]. Maximum 60 items. Amount is computed server-side"
    }
  }
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Items must contain at least one entry with a Description.",
  "Code": 400
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "The invoice total must be between $1 and $1,000,000. Computed total: $0.",
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

**403 Forbidden** — account-wide plan cap reached (see `MaxInvoicesAllowed` in [Plan Details](../account/plan.md)).

```json
{
  "Response": false,
  "Message": "Invoices Limit Reached. Your plan allows 100 invoice(s) across the whole account and you currently have 100. Please upgrade your plan to create more invoices",
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
- **💵 Money Is Recomputed:** `Subtotal`, `TaxAmount`, the fee breakdown and `Total` are always derived from `Items`, `DiscountAmount`, `TaxRate` and `PaymentMethod`. Values sent for them are ignored
- **💳 Fees Are Paid By The Recipient, And Only On A Card:** a `card` invoice's `Total` **includes** the surcharge and you receive `AmountBeforeFees` in full. It is a gross-up, not an addition: `AmountBeforeFees / (1 − fee)`, because the fees are charged as a percentage of what is actually charged. **`check` and `transfer` carry no fee at all** — nothing reaches the processor. On `recipient`, `Total` is the base and `FeeAmount` is what the surcharge would be if the payer takes the card
- **🔢 Atomic Numbering:** Invoice numbers are reserved with an atomic per-account counter, so parallel create calls always receive distinct consecutive numbers
- **🧊 Frozen Fees:** `ProcessingFeePercentage` and `MerchantFeePercentage` are copied from your plan at creation and never recalculated afterwards
- **📧 No Email:** This endpoint never notifies the recipient. Take `PublicLink` and distribute it however you want
- **📐 Limits:** Maximum 60 items, quantity up to 100, rate up to $1,000,000, and a total between $1 and $1,000,000
