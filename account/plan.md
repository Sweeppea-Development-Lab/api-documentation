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
- **Sharing & Execution Permissions:** `AllowSurveysSharing`, `AllowInvoicesSharing`, `AllowAgentsSharing` and `AllowDripCampaignsSharing` report whether the plan allows putting each module in front of the public — publishing a survey and its link/QR/embed, sending an invoice and taking its payment, activating an agent on a website or an entry page. Creating and viewing those modules is open to every account regardless of these flags. Unlike the numeric restrictions, a plan that does not carry the key reports `false`: these shipped switched off on every plan and are granted explicitly by an administrator.
- **Messaging Allowances:** `MaxEmailsPerMonth` and `MaxSmsPerMonth` are MONTHLY and account-wide, and `AllowEmailMessaging` / `AllowSmsMessaging` decide whether the plan may send at all. They are separate because SMS carries 10DLC registration, per-carrier daily caps and a quiet-hours regime that email does not, so a plan routinely sells one without the other. **`MaxSmsPerMonth` counts SEGMENTS, not messages:** a text longer than 160 characters, or one carrying a single emoji, is billed as more than one segment, so presenting this figure as "messages" overstates the allowance by up to 3x. `MaxEmailTemplatesAllowed` caps the saved email LAYOUTS, not the mail sent through them.
- **Usage:** live consumption paired with the ceilings in `Data.Settings`. `Emails` and `SmsSegments` are committed sends PLUS what campaigns in flight have already reserved — a launch holding its allowance has spent it as far as the next launch is concerned, so a caller reporting delivered-only will overstate what is left. `MessagingMonth` is the calendar month in the ACCOUNT'S OWN timezone, not UTC, which is why it can differ from the caller's month on the first and last day.
- **AI token usage is not reported** (`Notes.AiTokensUsageReported` is `false`). The monthly figure merges two separate ledgers with BYOM traffic excluded, and the platform reads it through one shared resolver that is never re-implemented; a second implementation here would drift from the number the account is actually billed against. The CEILING is reported as `Data.Settings.MaxAiTokensAllowed`.

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
      "MaxApiCallsPerMinute": 150,
      "MaxInvoicesAllowed": 100,
      "MaxSurveysAllowed": 3,
      "MaxAiTokensAllowed": 1000000,
      "MaxAgentsAllowed": 3,
      "MaxEmailsPerMonth": 1000,
      "MaxSmsPerMonth": 1000,
      "MaxEmailTemplatesAllowed": 5,
      "AllowSurveysSharing": false,
      "AllowInvoicesSharing": false,
      "AllowAgentsSharing": false,
      "AllowDripCampaignsSharing": false,
      "AllowEmailMessaging": false,
      "AllowSmsMessaging": false
    },
    "Primary": false,
    "Locked": true,
    "CreationDate": "2025-06-02T01:30:10.261Z",
    "__v": 0
  },
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 142,
    "MaxAPICalls": 500000
  },
  "Usage": {
    "Sweepstakes": 4,
    "MaxSweepstakes": 10,
    "ArchivedSweepstakes": 1,
    "Surveys": 2,
    "MaxSurveys": 3,
    "Invoices": 17,
    "MaxInvoices": 100,
    "Participants": 12840,
    "MaxParticipants": 500000,
    "ApiCalls": 142,
    "MaxApiCalls": 500000,
    "MaxApiCallsPerMinute": 150,
    "Agents": 1,
    "MaxAgents": 3,
    "Emails": 620,
    "MaxEmails": 1000,
    "SmsSegments": 0,
    "MaxSmsSegments": 1000,
    "MessagingMonth": "2026-09",
    "Breakdown": {
      "EntryPageParticipants": 12100,
      "AmoeParticipants": 740
    },
    "Notes": {
      "ArchivedCountTowardLimit": true,
      "UnlimitedWhenMaxIsZero": true,
      "SmsCountedInSegments": true,
      "MessagingIncludesReserved": true,
      "AiTokensUsageReported": false
    }
  }
}
```


**401 Unauthorized**

```json
{
  "Response": false,
  "Message": "Missing or invalid Bearer token. Send your API token in the Authorization header as: Authorization: Bearer YOUR_API_TOKEN",
  "Code": 401
}
```

**403 Forbidden - Invalid API Token**

```json
{
  "Response": false,
  "Message": "Invalid API token. It does not match any account.",
  "Code": 403
}
```

**403 Forbidden - Account Disabled**

```json
{
  "Response": false,
  "Message": "Your account is disabled or hibernating. Contact support for more information.",
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
