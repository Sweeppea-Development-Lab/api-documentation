# Create Rules via Wizard

Create official rules for a sweepstakes using the 14-step wizard flow. This endpoint takes structured wizard answers as input, generates the complete official rules HTML document server-side, and saves the result. The first rule created for a sweepstakes will be marked as primary.

## Endpoint

`POST /rules/createWizard`

## Description

This endpoint replicates the Official Rules Wizard from the Sweeppea frontend application. It follows a 14-step process (Steps A through N) that collects sweepstakes details and generates a complete legal document (HTML) for official rules.

Unlike the `/rules/create` endpoint which requires you to provide pre-built HTML content, this endpoint generates the document automatically from structured data. This is ideal for API integrations, MCP tools, and automated workflows.

**Wizard Steps:** A = Welcome, B = ARV Check, C = Alcohol, D = Promo Name, E = Start/End Dates, F = Prize Details, G = Prize Value, H = Winners/Entry Periods, I = Sponsor Info, J = Entry Method, K = Minimum Age, L = States Eligibility, M = Privacy Policy, N = Entry Page

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters - Required Fields

| Parameter | Type | Step | Description |
|-----------|------|------|-------------|
| `SweepstakesToken` | String (UUID v4) | - | The unique identifier of the target sweepstakes |
| `Arv` | Number (1\|2) | B | 1 = Total ARV >= $5,000, 2 = Total ARV < $5,000 |
| `AlcoholSweeps` | Number (1\|2) | C | 1 = Alcohol-related sweepstakes, 2 = Not alcohol-related. The 21+ requirement is triggered by the **sponsor producing or manufacturing alcohol** — a bar, a restaurant or a store that only resells it is not an alcohol sponsor for this purpose. Sending `1` forces `MinAgeOfParticipation` to `2`. |
| `SweepstakesName` | String (6-60 chars) | D | Official promotional name of the sweepstakes |
| `StartDate` | String (YYYY-MM-DD) | E | Sweepstakes start date |
| `StartTime` | String | E | Start time (e.g., "09:00 AM" or "14:00") |
| `StartTimezone` | String | E | Timezone (e.g., "US/Eastern", "EST", "CST") |
| `EndDate` | String (YYYY-MM-DD) | E | Sweepstakes end date |
| `EndTime` | String | E | End time (e.g., "11:59 PM" or "23:59") |
| `EndTimezone` | String | E | Timezone for end date |
| `PrizeDescription` | String (max 5000) | F | Full description of all prize(s) being offered |
| `PrizeIncludeTravel` | Boolean | F | true = Prize includes travel, false = No travel |
| `PrizeIsVehicle` | Boolean | F | true = Prize is a vehicle, false = Not a vehicle |
| `PrizeValue` | Number (> 0) | G | Approximate Retail Value of all prizes combined (USD) |
| `EntryPeriodSelector` | Number (1\|2) | H | 1 = Single drawing, 2 = Multiple entry periods |
| `SponsorName` | String | I | Legal name of the sweepstakes sponsor |
| `SponsorAddress` | String | I | Street address of the sponsor |
| `SponsorCity` | String | I | City where the sponsor is located |
| `SponsorState` | String | I | US state name or abbreviation |
| `SponsorZipCode` | String (5 digits) | I | 5-digit ZIP code (e.g., "33131") |
| `MethodOfEntry` | Number (1-8) | J | 1=Website, 2=SMS, 3=Social Media, 4=Other, 5=Purchase($1=1entry), 6=Purchase(1order=1entry), 7=Donation, 8=Subscription |
| `MinAgeOfParticipation` | Number (1\|2\|3) | K | 1 = 18+, 2 = 21+, 3 = 13+ (with parental consent). **Must be `2` when `AlcoholSweeps` is `1`** — an alcohol-related sweepstakes cannot be opened to participants under 21, and any other value is rejected with a `400`. |
| `StatesAbleToParticipate` | Number (1-10) | L | Geographic eligibility (see Eligibility Options below) |
| `PrivacyPolicyURL` | String (min 11 chars) | M | Full URL to privacy policy (must include http/https) |
| `SweeppeaEntryPage` | Number (1\|2\|3) | N | 1=Sweeppea entry page, 2=Custom entry page, 3=No entry page |

## Request Parameters - Conditional Fields

These fields are required only when certain conditions are met.

| Parameter | Type | Required When | Description |
|-----------|------|---------------|-------------|
| `WinnersToDraw` | Number (>= 1) | EntryPeriodSelector = 1 | Number of winners to draw |
| `WinnerDrawingDate` | String (YYYY-MM-DD) | EntryPeriodSelector = 1 | Date when winners will be drawn |
| `WinnerDrawingTime` | String | EntryPeriodSelector = 1 | Time of drawing |
| `WinnerDrawingTimezone` | String | EntryPeriodSelector = 1 | Timezone for drawing |
| `WinnerNotificationDate` | String (YYYY-MM-DD) | EntryPeriodSelector = 1 | Date when winners will be notified |
| `WinnerNotificationTime` | String | EntryPeriodSelector = 1 | Time of notification |
| `WinnerNotificationTimezone` | String | EntryPeriodSelector = 1 | Timezone for notification |
| `EntryPeriodItems` | Array | EntryPeriodSelector = 2 | Array of entry period objects (see structure below) |
| `SocialMediaEntryDescription` | String | MethodOfEntry = 3 | Description of social media entry steps |
| `OtherDescription` | String | MethodOfEntry = 4 | Description of alternative entry method |
| `SponsorEcommerceStoreURLA` | String | MethodOfEntry = 5 | eCommerce store URL for purchase entry ($1 = 1 entry) |
| `SponsorEcommerceStoreURLB` | String | MethodOfEntry = 6 | eCommerce store URL for purchase entry (1 order = 1 entry) |
| `SponsorDonationsAcceptancePageURL` | String | MethodOfEntry = 7 | Donations acceptance page URL |
| `SponsorEcommerceStoreURLC` | String | MethodOfEntry = 8 | eCommerce store URL for subscription entry |
| `TotalNumberOfEntriesAwardedAMOE` | Number (>= 1) | MethodOfEntry = 5, 6, 7, or 8 | Entries awarded per AMOE submission |
| `LimitOrMaxNumberOfEntriesAMOE` | Number (>= 1) | MethodOfEntry = 5, 6, 7, or 8 | Maximum AMOE entries per person |
| `ListOfStates` | Array of Strings | StatesAbleToParticipate = 4 | Only real US jurisdictions are accepted: the 50 states, the District of Columbia and Puerto Rico, by full name or two-letter abbreviation, in any case. Abbreviations are normalized to the full name in the generated document. Unknown values are rejected with a `400` naming every one of them. Example: `["Florida", "California"]` or `["FL", "CA"]` |
| `CustomEntryPage` | String (min 11 chars) | SweeppeaEntryPage = 2 | URL of the custom entry page |

## Request Parameters - Optional Fields

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `SponsorTelephone` | String | "" | Sponsor phone number |
| `SponsorEmail` | String | "" | Sponsor email address |
| `SponsorOfferingMultiplier` | Number (1\|2) | 2 | 1=Yes, 2=No. Is sponsor offering entry multipliers? |
| `SponsorAwardingBonusEmailSocial` | Number (1\|2) | 2 | 1=Yes, 2=No. Bonus entries for email/social shares? |
| `SponsorAskingToSubmitVideo` | Number (1\|2) | 2 | 1=Yes, 2=No. Participants submit user-generated video? |
| `RulesLanguage` | String | "en" | Language code for the rules document |

## Geographic Eligibility Options (StatesAbleToParticipate)

| Value | Description |
|-------|-------------|
| 1 | 48 contiguous US states + DC |
| 2 | 50 US states + DC |
| 3 | 50 US states + DC + US Virgin Islands + Puerto Rico |
| 4 | Only the states listed in `ListOfStates` |
| 5 | 50 US states + DC, **excluding Florida and New York** |
| 6 | 50 US states + DC, **excluding Florida, New York and Rhode Island** |
| 7 | 50 US states + DC + Canada |
| 8 | 50 US states + DC + Canada, **excluding Florida and New York** |
| 9 | 50 US states + DC + Canada, **excluding Florida, New York and Rhode Island** |
| 10 | Canada only |

## Request Example

When `EntryPeriodSelector = 2`, provide an array of entry period objects:

```json
{
  "EntryPeriodItems": [
    {
      "data": {
        "StartDate": "2026-03-01",
        "StartTime": "09:00 AM",
        "EndDate": "2026-03-07",
        "EndTime": "11:59 PM",
        "DrawingDate": "2026-03-08",
        "WinnersDrawn": 1,
        "NotificationDate": "2026-03-09",
        "PrizeDescription": "One (1) $100 Gift Card",
        "PrizeValue": 100
      }
    },
    {
      "data": {
        "StartDate": "2026-03-08",
        "StartTime": "12:00 AM",
        "EndDate": "2026-03-14",
        "EndTime": "11:59 PM",
        "DrawingDate": "2026-03-15",
        "WinnersDrawn": 2,
        "NotificationDate": "2026-03-16",
        "PrizeDescription": "Two (2) $50 Gift Cards",
        "PrizeValue": 100
      }
    }
  ]
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/rules/createWizard" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "YOUR_SWEEPSTAKES_TOKEN",
    "Arv": 2,
    "AlcoholSweeps": 2,
    "SweepstakesName": "Summer Giveaway 2026 Sweepstakes",
    "StartDate": "2026-03-01",
    "StartTime": "09:00 AM",
    "StartTimezone": "US/Eastern",
    "EndDate": "2026-04-01",
    "EndTime": "11:59 PM",
    "EndTimezone": "US/Eastern",
    "PrizeDescription": "One (1) $500 Visa eGift Card.",
    "PrizeIncludeTravel": false,
    "PrizeIsVehicle": false,
    "PrizeValue": 500,
    "EntryPeriodSelector": 1,
    "WinnersToDraw": 1,
    "WinnerDrawingDate": "2026-04-05",
    "WinnerDrawingTime": "10:00 AM",
    "WinnerDrawingTimezone": "US/Eastern",
    "WinnerNotificationDate": "2026-04-07",
    "WinnerNotificationTime": "10:00 AM",
    "WinnerNotificationTimezone": "US/Eastern",
    "SponsorName": "Acme Corporation",
    "SponsorAddress": "123 Main Street",
    "SponsorCity": "Miami",
    "SponsorState": "Florida",
    "SponsorZipCode": "33131",
    "SponsorTelephone": "3051234567",
    "SponsorEmail": "info@acme.com",
    "MethodOfEntry": 1,
    "MinAgeOfParticipation": 1,
    "StatesAbleToParticipate": 1,
    "PrivacyPolicyURL": "https://acme.com/privacy",
    "SweeppeaEntryPage": 1
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/rules/createWizard', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: 'YOUR_SWEEPSTAKES_TOKEN',
    Arv: 2,
    AlcoholSweeps: 2,
    SweepstakesName: 'Summer Giveaway 2026 Sweepstakes',
    StartDate: '2026-03-01',
    StartTime: '09:00 AM',
    StartTimezone: 'US/Eastern',
    EndDate: '2026-04-01',
    EndTime: '11:59 PM',
    EndTimezone: 'US/Eastern',
    PrizeDescription: 'One (1) $500 Visa eGift Card.',
    PrizeIncludeTravel: false,
    PrizeIsVehicle: false,
    PrizeValue: 500,
    EntryPeriodSelector: 1,
    WinnersToDraw: 1,
    WinnerDrawingDate: '2026-04-05',
    WinnerDrawingTime: '10:00 AM',
    WinnerDrawingTimezone: 'US/Eastern',
    WinnerNotificationDate: '2026-04-07',
    WinnerNotificationTime: '10:00 AM',
    WinnerNotificationTimezone: 'US/Eastern',
    SponsorName: 'Acme Corporation',
    SponsorAddress: '123 Main Street',
    SponsorCity: 'Miami',
    SponsorState: 'Florida',
    SponsorZipCode: '33131',
    SponsorTelephone: '3051234567',
    SponsorEmail: 'info@acme.com',
    MethodOfEntry: 1,
    MinAgeOfParticipation: 1,
    StatesAbleToParticipate: 1,
    PrivacyPolicyURL: 'https://acme.com/privacy',
    SweeppeaEntryPage: 1
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests
import json

url = "https://api-v3.sweeppea.com/rules/createWizard"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "YOUR_SWEEPSTAKES_TOKEN",
    "Arv": 2,
    "AlcoholSweeps": 2,
    "SweepstakesName": "Summer Giveaway 2026 Sweepstakes",
    "StartDate": "2026-03-01",
    "StartTime": "09:00 AM",
    "StartTimezone": "US/Eastern",
    "EndDate": "2026-04-01",
    "EndTime": "11:59 PM",
    "EndTimezone": "US/Eastern",
    "PrizeDescription": "One (1) $500 Visa eGift Card.",
    "PrizeIncludeTravel": False,
    "PrizeIsVehicle": False,
    "PrizeValue": 500,
    "EntryPeriodSelector": 1,
    "WinnersToDraw": 1,
    "WinnerDrawingDate": "2026-04-05",
    "WinnerDrawingTime": "10:00 AM",
    "WinnerDrawingTimezone": "US/Eastern",
    "WinnerNotificationDate": "2026-04-07",
    "WinnerNotificationTime": "10:00 AM",
    "WinnerNotificationTimezone": "US/Eastern",
    "SponsorName": "Acme Corporation",
    "SponsorAddress": "123 Main Street",
    "SponsorCity": "Miami",
    "SponsorState": "Florida",
    "SponsorZipCode": "33131",
    "SponsorTelephone": "3051234567",
    "SponsorEmail": "info@acme.com",
    "MethodOfEntry": 1,
    "MinAgeOfParticipation": 1,
    "StatesAbleToParticipate": 1,
    "PrivacyPolicyURL": "https://acme.com/privacy",
    "SweeppeaEntryPage": 1
}

response = requests.post(url, headers=headers, data=json.dumps(payload))
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "RulesToken": "bc225a85-c16f-4339-a1d6-d0c6d62fa18d",
    "SweepstakesToken": "1770a839-abeb-42b4-b003-888073ba1e9b",
    "Title": "SummerGiveaway-Official-Rules-02/17/2026-54321",
    "Primary": true,
    "CreationDate": "2026-02-17T12:00:00.000Z"
  },
  "Warnings": [],
  "Message": "Official rules created successfully via wizard."
}
```

### Warnings

`Warnings` is **always present** on a `200`, and is an empty array when there is
nothing to report. A warning never means the rules document failed to save — it
means the document was published but something else in the sweepstakes does not
match what it now promises, and only the client can fix it.

| Code | When it is returned |
|------|---------------------|
| `AMOE_PAGE_NOT_ACTIVE` | `MethodOfEntry` is 5, 6, 7 or 8, so the document states that a free alternative method of entry is available, but the sweepstakes' AMOE page is switched off and participants following the free route cannot enter. The AMOE page is OFF on every newly created sweepstakes and the wizard does not turn it on. Fix it with `POST /entrypage/update` and `{ "ActivateAmoeSwitch": true }`. |
| `AMOE_ENTRIES_MISMATCH` | `TotalNumberOfEntriesAwardedAMOE` in the published document differs from `AmoeEntries` on the entry page, so the page awards a different number of entries than the rules promise. Fix it with `POST /entrypage/update` and `{ "AmoeEntries": <value> }`. |

```json
{
  "Response": true,
  "Data": { "RulesToken": "bc225a85-c16f-4339-a1d6-d0c6d62fa18d", "...": "..." },
  "Warnings": [
    {
      "Code": "AMOE_PAGE_NOT_ACTIVE",
      "Field": "MethodOfEntry",
      "Message": "These official rules state that a free alternative method of entry (AMOE) is available, but the AMOE page of this sweepstakes is currently switched OFF, so participants who follow the free route will not be able to enter.",
      "Action": "Turn it on in the app under Entry Page > AMOE, or call POST /entrypage/update with { \"SweepstakesToken\": \"...\", \"ActivateAmoeSwitch\": true }."
    }
  ],
  "Message": "Official rules created successfully via wizard, with warnings that need your attention."
}
```

**400 Bad Request**

Validation errors include `Field`, `WizardStep`, and `Hint` for easy debugging:

```json
{
  "Response": false,
  "Message": "SweepstakesName is required. The official promotional name of the sweepstakes.",
  "Code": 400,
  "Field": "SweepstakesName",
  "WizardStep": "D",
  "Hint": "Must be between 6 and 60 characters"
}
```

```json
{
  "Response": false,
  "Message": "WinnersToDraw is required when EntryPeriodSelector is 1. Number of winners to draw.",
  "Code": 400,
  "Field": "WinnersToDraw",
  "WizardStep": "H",
  "Hint": "Must be a number >= 1"
}
```

```json
{
  "Response": false,
  "Message": "MethodOfEntry must be a number between 1 and 8.",
  "Code": 400,
  "Field": "MethodOfEntry",
  "WizardStep": "J",
  "Hint": "1 = Online Entry Form, 2 = Text-to-Win (SMS), 3 = Social Media, 4 = Other, 5 = Purchase + AMOE (eCommerce A), 6 = Purchase + AMOE (eCommerce B), 7 = Donation + AMOE, 8 = Purchase + AMOE (eCommerce C)"
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
  "Message": "Sweepstakes not found. Ensure the SweepstakesToken belongs to the authenticated user.",
  "Code": 404,
  "Field": "SweepstakesToken"
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
