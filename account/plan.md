# Fetch Plan Details

Fetch Plan Details API endpoint for Sweeppea. Retrieves comprehensive plan information including pricing, limits, and features based on the authenticated user's plan token.

## Endpoint

`POST account/plan`

## Description

This endpoint retrieves detailed information about the user's plan using the ApiToken for authentication. It returns comprehensive plan details including pricing, subscription frequency, limits for sweepstakes, storage, participants, API calls, and additional features.

## Notes

- **API Token Validation:** The Bearer token must be valid and associated with an existing user account.
- **Account Status Check:** The user's account Status must be `true` (enabled). If the account is disabled (`Status = false`), the request will be rejected with a 403 error.
- **Domain Access Control:** The user must have a wildcard domain (`*`) configured in their API domains. This allows API access from any domain. Without the wildcard domain, the request will be rejected with a 403 error.

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/account/plan" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/account/plan', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_TOKEN',
    'Content-Type': 'application/json'
  }
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/account/plan"
headers = {
    "Authorization": "Bearer YOUR_API_TOKEN",
    "Content-Type": "application/json"
}

response = requests.post(url, headers=headers)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "_id": "683cfea2026928b08b64badc",
    "PlanToken": "PLAN-TOKEN",
    "Name": "Plan Name",
    "Description": "Plan Description",
    "Settings": {
      "ChargePerSweepstakes": false,
      "Frequency": "Daily",
      "SubscriptionPrice": 0,
      "SubscriptionPriceDiscount": 0,
      "FixedPrice": 0,
      "FixedPriceDiscount": 0,
      "CostPerGigaByte": 1,
      "ChargeDTAfterGB": 50,
      "ExtraCredits": 0,
      "WalletMinToDeposit": 500,
      "WalletMinToWithdraw": 10,
      "MerchantProcessorFee": 3,
      "SweeppeaCommision": 3,
      "ChargeTwilioNumber": true,
      "TwilioNumberPrice": 99,
      "PaidModuleCreateSweepstakes": false,
      "PaidModuleCreateEntryPage": false,
      "PaidModuleCreateOfficialRules": false,
      "PaidModuleManageParticipants": false,
      "PaidModuleSendMessages": false,
      "PaidModuleDrawWinners": false,
      "PaidModuleSeeReports": false,
      "PaidModuleManageDomains": false,
      "PaidModuleManageCoupons": false,
      "PaidModuleAccessDrive": false,
      "PaidModuleAccessCalendar": false,
      "PaidModuleManageNotes": false,
      "PaidModuleCreateSurveys": false,
      "PaidModuleAccessAPI": false,
      "PaidModuleManageDripMarketing": false,
      "MaxSweepstakesAllowed": 10,
      "MaxStorageSize": 5000,
      "MaxParticipantsAllowed": 500000,
      "MaxApiCallsAllowed": 500000,
      "MaxInvoicesAllowed": 100,
      "MaxSurveysAllowed": 3,
      "MaxAiTokensAllowed": 1000000
    },
    "Primary": false,
    "Locked": true,
    "CreationDate": "2025-06-02T01:30:10.261Z"
  },
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 142,
    "MaxAPICalls": 500000
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

**403 Forbidden - Invalid API Token**

```json
{
  "Response": false,
  "Message": "Invalid API Token",
  "Code": 403
}
```

**403 Forbidden - Account Disabled**

```json
{
  "Response": false,
  "Message": "Account is disabled. Please contact support.",
  "Code": 403
}
```

**403 Forbidden - Domain Access Restricted**

```json
{
  "Response": false,
  "Message": "API access restricted. Wildcard domain (*) required.",
  "Code": 403
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Plan Not Found",
  "Code": 404
}
```
