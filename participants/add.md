# Add Participant

Add a participant to a sweepstakes programmatically via the Sweeppea API.

## Endpoint

`POST /participants/add`

## Description

This endpoint allows you to add participants to your sweepstakes. Use this to manage your participants & groups data programmatically through the Sweeppea API.

> **Important Note:**
> - Field names with multiple words (e.g., "First Name") must have spaces replaced with underscores (e.g., "First_Name").
> - Fields must match exactly with the Entry Page fields in the same order and structure.
> - To obtain the correct field mapping for your sweepstakes, consult the [Fetch Entry Page Fields](https://apidocs.sweeppea.com/entrypage/fields.html) endpoint.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter                        | Type   | Required | Description                                                   |
|----------------------------------|--------|----------|---------------------------------------------------------------|
| `lang`                           | String | Yes      | Language code (e.g., `"EN"`)                                  |
| `source`                         | String | Yes      | Entry source (e.g., `"api"`)                                  |
| `sweepstakesToken`               | String | Yes      | UUID v4 identifier for the sweepstakes                        |
| `entryPageFields`                | Object | Yes      | Object containing all entry page field data                   |
| `entryPageFields.KeyPhoneNumber` | String | No       | Participant's phone number                                    |
| `entryPageFields.KeyEmail`       | String | No       | Participant's email address                                   |
| `entryPageFields.BonusEntries`   | Number | No       | Number of bonus entries (default: 0)                          |
| `entryPageFields.Fields`         | Object | Yes      | Object containing the participant's entry page field values   |

## Request Example

```json
{
  "lang": "EN",
  "source": "api",
  "sweepstakesToken": "uuid-v4-string",
  "entryPageFields": {
    "KeyPhoneNumber": "5551234567",
    "KeyEmail": "john.doe@example.com",
    "BonusEntries": 0,
    "Fields": {
      "First_Name": "John",
      "Last_Name": "Doe",
      "City": "New York",
      "State": "NY"
    }
  }
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/participants/add" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "lang": "EN",
    "source": "api",
    "sweepstakesToken": "uuid-v4-string",
    "entryPageFields": {
      "KeyPhoneNumber": "5551234567",
      "KeyEmail": "john.doe@example.com",
      "BonusEntries": 0,
      "Fields": {
        "First_Name": "John",
        "Last_Name": "Doe",
        "City": "New York",
        "State": "NY"
      }
    }
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/participants/add', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    lang: "EN",
    source: "api",
    sweepstakesToken: "uuid-v4-string",
    entryPageFields: {
      KeyPhoneNumber: "5551234567",
      KeyEmail: "john.doe@example.com",
      BonusEntries: 0,
      Fields: {
        "First_Name": "John",
        "Last_Name": "Doe",
        "City": "New York",
        "State": "NY"
      }
    }
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/participants/add"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "lang": "EN",
    "source": "api",
    "sweepstakesToken": "uuid-v4-string",
    "entryPageFields": {
        "KeyPhoneNumber": "5551234567",
        "KeyEmail": "john.doe@example.com",
        "BonusEntries": 0,
        "Fields": {
            "First_Name": "John",
            "Last_Name": "Doe",
            "City": "New York",
            "State": "NY"
        }
    }
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "(OK) Participant successfully added to your sweepstakes."
}
```

**400 Bad Request**

```json
{
  "Response": false,
  "Message": "Invalid parameters in body object, read documentation.",
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

**403 Forbidden** (API call limit reached)

```json
{
  "Response": false,
  "Telemetry": {
    "DataConsumed": 1234,
    "APICalls": 1000,
    "MaxAPICalls": 1000
  },
  "Message": "You have reached the maximum API calls allowed for this month. Contact support for more information."
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "User Not Found",
  "Code": 404
}
```
