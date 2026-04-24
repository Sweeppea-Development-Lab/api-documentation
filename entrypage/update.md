# Update Entry Page Settings

Partially update the settings of an entry page owned by the authenticated user. Send between 1 and 5 fields per request. All values are strictly type-validated.

## Endpoint

`POST /entrypage/update`

## Description

This endpoint updates one to five settings fields of an entry page identified by `SweepstakesToken`. The entry page must exist and belong to the authenticated user. Only fields listed in the allowed whitelist are accepted, and each value is strictly type-validated before being applied. Updates are applied as a partial `$set` on the `Settings` subdocument — no other data is affected.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `SweepstakesToken` | String (UUID v4) | Yes | Unique identifier of the sweepstakes owning the entry page |
| *field name(s)* | Mixed | Yes (1–5) | One to five allowed settings fields to update (see table below) |

## Allowed Fields

| Field | Type | Notes |
|-------|------|-------|
| `EntryPageHeadline` | String |  |
| `EntryPageDescription` | String |  |
| `EntryPageAbbreviatedRules` | String |  |
| `EntryPageWidth` | Number | Coherent numeric value |
| `EntryPageWidthMeasure` | String | Only `"%"` or `"px"` |
| `EntryPageBorder` | String | e.g. `"0px dotted black"` |
| `EntryPageBackgroundColor` | Object | `{ "hexa": "#FFF" }` |
| `EntryPageBackgroundInnerColor` | Object | `{ "hexa": "#FFF" }` |
| `EntryPageMarginTop` | Number |  |
| `EntryPageMarginBottom` | Number |  |
| `EntryPageRadius` | Number |  |
| `EntryPageTextColor` | Object | `{ "hexa": "#000" }` |
| `EntryPageButtonColor` | Object | `{ "hexa": "#0275d8" }` |
| `EntryPageShowOverlay` | Boolean |  |
| `BonusEntriesSwitch` | Boolean |  |
| `BonusEntriesValue` | Number |  |
| `EmailOptInSwitch` | Boolean |  |
| `EmailOptInMessage` | String |  |
| `TermsConditionsSwitch` | Boolean |  |
| `TermsConditionsMessage` | String |  |
| `SelectedOfficialRules` | String (UUID v4) | Token of the official rules document |
| `ConfirmationPageHeadline` | String |  |
| `ConfirmationPageDescription` | String |  |
| `WebExpirationMessage` | String |  |
| `ExternalConfirmationPageURI` | String |  |
| `ConfirmationYoutubeUrl` | String |  |
| `ActivateWinnersSwitch` | Boolean |  |
| `WinnersPageHeadline` | String |  |
| `WinnersPageDescription` | String |  |
| `ExternalWinnersPageURI` | String |  |
| `ActivateAgeGateSwitch` | Boolean |  |
| `AgeGateHeadline` | String |  |
| `AgeGateDescription` | String |  |
| `AgeGateMinAge` | Number |  |
| `AgeGateBackgroundColor` | Object | `{ "hex": "#FFF" }` |
| `AgeGateTextColor` | Object | `{ "hex": "#000" }` |
| `ActivateAmoeSwitch` | Boolean |  |
| `AmoeHeadline` | String |  |
| `AmoeDescription` | String |  |
| `AmoeEntries` | Number |  |
| `EnableInternationalAMOEForm` | Boolean |  |
| `GeoLocation` | Boolean |  |
| `GeoLocationIsRequiredToRenderPage` | Boolean |  |
| `AllowParticipantsWithinFences` | Boolean |  |
| `CollectStatistics` | Boolean |  |
| `EnableShareWidget` | Boolean |  |
| `EnableProgressBar` | Boolean |  |
| `EnableSweepstakesCountdown` | Boolean |  |
| `EnableNumberOfParticipants` | Boolean |  |
| `EnableSocialWidget` | Boolean |  |
| `FollowFacebookSwitch` | Boolean |  |
| `FollowXSwitch` | Boolean |  |
| `FollowInstagramSwitch` | Boolean |  |
| `FollowTikTokSwitch` | Boolean |  |
| `FollowLinkedInSwitch` | Boolean |  |
| `FollowPinterestSwitch` | Boolean |  |
| `FollowThreadsSwitch` | Boolean |  |
| `FollowRedditSwitch` | Boolean |  |
| `FollowSnapchatSwitch` | Boolean |  |
| `FollowYoutubeSwitch` | Boolean |  |
| `FollowTwitchSwitch` | Boolean |  |
| `BothSocialShareParticipantsAwardedSwitch` | Boolean |  |
| `BothEmailShareParticipantsAwardedSwitch` | Boolean |  |
| `BonusEntriesOnRegister` | Number |  |
| `BonusEntriesOnShareFacebook` | Number |  |
| `BonusEntriesOnShareX` | Number |  |
| `BonusEntriesOnShareInstagram` | Number |  |
| `BonusEntriesOnShareTikTok` | Number |  |
| `BonusEntriesOnShareLinkedIn` | Number |  |
| `BonusEntriesOnSharePinterest` | Number |  |
| `BonusEntriesOnShareThreads` | Number |  |
| `BonusEntriesOnShareReddit` | Number |  |
| `BonusEntriesOnShareSnapchat` | Number |  |
| `BonusEntriesOnShareWhatsApp` | Number |  |
| `BonusEntriesOnShareTelegram` | Number |  |
| `BonusEntriesOnShareGenericLink` | Number |  |
| `BonusEntriesOnShareEmail` | Number |  |
| `BonusEntriesFollowFacebook` | Number |  |
| `BonusEntriesFollowX` | Number |  |
| `BonusEntriesFollowInstagram` | Number |  |
| `BonusEntriesFollowTikTok` | Number |  |
| `BonusEntriesFollowLinkedIn` | Number |  |
| `BonusEntriesFollowPinterest` | Number |  |
| `BonusEntriesFollowThreads` | Number |  |
| `BonusEntriesFollowReddit` | Number |  |
| `BonusEntriesFollowSnapchat` | Number |  |
| `BonusEntriesFollowYoutube` | Number |  |
| `BonusEntriesFollowTwitch` | Number |  |
| `SponsorFacebookProfile` | String or null | URL |
| `SponsorXProfile` | String or null | URL |
| `SponsorInstagramProfile` | String or null | URL |
| `SponsorTikTokProfile` | String or null | URL |
| `SponsorLinkedInProfile` | String or null | URL |
| `SponsorPinterestProfile` | String or null | URL |
| `SponsorThreadsProfile` | String or null | URL |
| `SponsorRedditProfile` | String or null | URL |
| `SponsorSnapchatProfile` | String or null | URL |
| `SponsorYoutubeProfile` | String or null | URL |
| `SponsorTwitchProfile` | String or null | URL |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "EntryPageHeadline": "Win Big This Summer!",
  "BonusEntriesSwitch": true,
  "BonusEntriesValue": 5
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/entrypage/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "uuid-v4-string",
    "EntryPageHeadline": "Win Big This Summer!",
    "BonusEntriesSwitch": true,
    "BonusEntriesValue": 5
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/entrypage/update', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: 'uuid-v4-string',
    EntryPageHeadline: 'Win Big This Summer!',
    BonusEntriesSwitch: true,
    BonusEntriesValue: 5
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/entrypage/update"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "SweepstakesToken": "uuid-v4-string",
    "EntryPageHeadline": "Win Big This Summer!",
    "BonusEntriesSwitch": True,
    "BonusEntriesValue": 5
}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "Entry Page Settings Updated Successfully",
  "UpdatedFields": ["EntryPageHeadline", "BonusEntriesSwitch", "BonusEntriesValue"],
  "SweepstakesToken": "uuid-v4-string"
}
```

**400 Bad Request** — No fields provided

```json
{
  "Response": false,
  "Message": "No Fields to Update. Provide between 1 and 5 fields to update.",
  "Hint": "Allowed fields: EntryPageHeadline, ...",
  "Code": 400
}
```

**400 Bad Request** — Too many fields

```json
{
  "Response": false,
  "Message": "Too Many Fields to Update. Maximum allowed per request is 5.",
  "Code": 400
}
```

**400 Bad Request** — Disallowed field

```json
{
  "Response": false,
  "Message": "Field 'SomeField' is Not Allowed or Does Not Exist",
  "Hint": "Allowed fields: EntryPageHeadline, ...",
  "Code": 400
}
```

**400 Bad Request** — Type validation

```json
{
  "Response": false,
  "Message": "BonusEntriesValue must be a number",
  "Code": 400
}
```

**400 Bad Request** — Missing/invalid SweepstakesToken

```json
{
  "Response": false,
  "Message": "SweepstakesToken is Required and Must be a Valid UUID v4",
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

**403 Forbidden** — Account disabled

```json
{
  "Response": false,
  "Message": "Account is Disabled",
  "Code": 403
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Entry Page Not Found or Access Denied",
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

- **Partial Updates:** Send only the fields you want to change — between 1 and 5 per request.
- **Ownership Verification:** The entry page must exist and belong to the authenticated user.
- **Type Validation:** Every value is strictly validated against its expected type before being saved.
- **Measure Field:** `EntryPageWidthMeasure` only accepts `"%"` or `"px"`.
- **Color Fields:** Must be objects — `{ "hexa": "#FFF" }` for most, `{ "hex": "#FFF" }` for AgeGate colors.
- **UUID Fields:** `SelectedOfficialRules` must be a valid UUID v4 (token of an existing rules document).
- **URL Fields:** Sponsor profile fields accept a URL string or `null`.
- **Max 5 Fields:** Requests with more than 5 fields will be rejected.
- **Not Found:** Returns 404 if the entry page doesn't exist or belongs to another user.
