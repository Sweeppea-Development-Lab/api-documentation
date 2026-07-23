# Update Rules

Update existing official rules for a specific sweepstakes. Only the rule owner can update their rules.

## Endpoint

`PUT /rules/update`

## Description

This endpoint updates an existing official rule document for a sweepstakes. The rule must exist and belong to the authenticated user. You can update the title, document content, and abbreviated rules for Shopify integration.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Max Length | Description |
|-----------|------|----------|------------|-------------|
| `RulesToken` | String (UUID v4) | Yes | 36 | The unique identifier of the rule to update |
| `SweepstakesToken` | String (UUID v4) | Yes | 36 | The unique identifier of the sweepstakes |
| `Title` | String | No | 100 | The title of the official rules |
| `DocumentContent` | String | No | 1,000,000 | The full HTML content of the official rules |
| `AbbreviatedRulesForShopify` | String | No | 1,000,000 | Abbreviated rules for Shopify integration. The legacy field name `AbbrebiatedRulesForShopify` (historical typo) is also accepted for backward compatibility |

**Note:** At least one optional field must be provided to update the rule.

**Note:** Responses include both `AbbreviatedRulesForShopify` (preferred) and `AbbrebiatedRulesForShopify` (legacy) with the same value, so existing integrations keep working.

## Code Examples

### cURL

```bash
curl -X PUT "https://api-v3.sweeppea.com/rules/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "RulesToken": "YOUR_RULES_TOKEN",
    "SweepstakesToken": "YOUR_SWEEPSTAKES_TOKEN",
    "Title": "Updated Official Rules",
    "DocumentContent": "<p>Updated HTML content of official rules...</p>",
    "AbbreviatedRulesForShopify": "Updated abbreviated rules for Shopify"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/rules/update', {
  method: 'PUT',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    RulesToken: 'YOUR_RULES_TOKEN',
    SweepstakesToken: 'YOUR_SWEEPSTAKES_TOKEN',
    Title: 'Updated Official Rules',
    DocumentContent: '<p>Updated HTML content of official rules...</p>',
    AbbreviatedRulesForShopify: 'Updated abbreviated rules for Shopify'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/rules/update"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "RulesToken": "YOUR_RULES_TOKEN",
    "SweepstakesToken": "YOUR_SWEEPSTAKES_TOKEN",
    "Title": "Updated Official Rules",
    "DocumentContent": "<p>Updated HTML content of official rules...</p>",
    "AbbreviatedRulesForShopify": "Updated abbreviated rules for Shopify"
}

response = requests.put(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "Rule Updated Successfully",
  "Data": {
    "RulesToken": "bc225a85-c16f-4339-a1d6-d0c6d62fa18d",
    "SweepstakesToken": "1770a839-abeb-42b4-b003-888073ba1e9b",
    "CreationDate": "2026-01-23T11:30:22.979Z",
    "Title": "Updated Official Rules",
    "Metadata": {},
    "DocumentContent": "<p>Updated HTML content of official rules...</p>",
    "AbbrebiatedRulesForShopify": "Updated abbreviated rules for Shopify",
    "AbbreviatedRulesForShopify": "Updated abbreviated rules for Shopify",
    "EntryPeriods": [],
    "Views": [],
    "Status": false,
    "Primary": true,
    "DataUsedToTrainLLModel": false
  }
}
```

**400 Bad Request**

All validation errors include a `Help` object listing every accepted parameter.

```json
{
  "Response": false,
  "Message": "RulesToken is Required",
  "Code": 400,
  "Help": {
    "ExpectedBody": {
      "RulesToken": "string (required) — UUID of the rule to update",
      "SweepstakesToken": "string (required) — UUID of the sweepstakes",
      "Title": "string (optional) — Rule title (max 100 characters)",
      "DocumentContent": "string (optional) — Rule content (max 1,000,000 characters)",
      "AbbreviatedRulesForShopify": "string (optional) — Abbreviated rules for Shopify (max 1,000,000 characters). Legacy alias \"AbbrebiatedRulesForShopify\" is also accepted for backward compatibility"
    }
  }
}
```

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
  "Message": "No Fields to Update",
  "Code": 400
}
```

```json
{
  "Response": false,
  "Message": "Title Must Be a Non-Empty String",
  "Code": 400
}
```

```json
{
  "Response": false,
  "Message": "Title Exceeds Maximum Length of 100 Characters",
  "Code": 400
}
```

```json
{
  "Response": false,
  "Message": "DocumentContent Must Be a String",
  "Code": 400
}
```

```json
{
  "Response": false,
  "Message": "DocumentContent Exceeds Maximum Length of 1000000 Characters",
  "Code": 400
}
```

```json
{
  "Response": false,
  "Message": "AbbreviatedRulesForShopify Must Be a String",
  "Code": 400
}
```

```json
{
  "Response": false,
  "Message": "AbbreviatedRulesForShopify Exceeds Maximum Length of 1000000 Characters",
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

```json
{
  "Response": false,
  "Message": "Rule Not Found or Access Denied",
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
