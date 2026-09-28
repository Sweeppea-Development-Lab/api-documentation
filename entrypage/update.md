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
| `EntryPageBorder` | String | `"<0-99>px <style> <color>"` — style one of `none`, `solid`, `dotted`, `dashed`, `double`, `groove`, `ridge`, `inset`, `outset`, `hidden`; color a name or hex. e.g. `"0px dotted black"`, `"1px solid #333333"` |
| `EntryPageBackgroundColor` | String \| Object | Page background. Hex string (`"#1A2B3C"`, `"#1A2B3C80"`) or object `{ "hexa": "#1A2B3CFF" }` / `{ "hex": "#1A2B3C" }` / `{ "rgba": { "r": 26, "g": 43, "b": 60, "a": 1 } }`. Stored rebuilt in the full picker shape. |
| `EntryPageBackgroundInnerColor` | String \| Object | Color of the block that wraps the form — rendered when `EntryPageBackgroundScope` is `"custom"`. Hex string (`"#1A2B3C"`, `"#1A2B3C80"`) or object `{ "hexa": "#1A2B3CFF" }` / `{ "hex": "#1A2B3C" }` / `{ "rgba": { "r": 26, "g": 43, "b": 60, "a": 1 } }`. Stored rebuilt in the full picker shape. |
| `EntryPageBackgroundScope` | String | Where the background color applies: `"outside"` (default — the color fills the page around the form, the form block stays white), `"full"` (the whole page including the form block — ideal to embed on a dark website), `"custom"` (the form block uses `EntryPageBackgroundInnerColor`). On a dark block the text and fields of the form turn light automatically. |
| `EntryPageMarginTop` | Number |  |
| `EntryPageMarginBottom` | Number |  |
| `EntryPageRadius` | Number |  |
| `EntryPageTextColor` | String \| Object | Hex string (`"#1A2B3C"`, `"#1A2B3C80"`) or object `{ "hexa": "#1A2B3CFF" }` / `{ "hex": "#1A2B3C" }` / `{ "rgba": { "r": 26, "g": 43, "b": 60, "a": 1 } }`. Stored rebuilt in the full picker shape. |
| `EntryPageButtonColor` | String \| Object | Hex string (`"#1A2B3C"`, `"#1A2B3C80"`) or object `{ "hexa": "#1A2B3CFF" }` / `{ "hex": "#1A2B3C" }` / `{ "rgba": { "r": 26, "g": 43, "b": 60, "a": 1 } }`. Stored rebuilt in the full picker shape. |
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
| `AgeGateMinAge` | Number (1\|2\|3) | **A CODE, NOT AN AGE IN YEARS.** `1` = 13 years or older, `2` = 18 years or older, `3` = 21 years or older. The platform default is `3` (21+). Any other value is rejected with a `400`. |
| `AgeGateBackgroundColor` | String \| Object | Hex string (`"#1A2B3C"`, `"#1A2B3C80"`) or object `{ "hexa": "#1A2B3CFF" }` / `{ "hex": "#1A2B3C" }` / `{ "rgba": { "r": 26, "g": 43, "b": 60, "a": 1 } }`. Stored rebuilt in the full picker shape. |
| `AgeGateTextColor` | String \| Object | Hex string (`"#1A2B3C"`, `"#1A2B3C80"`) or object `{ "hexa": "#1A2B3CFF" }` / `{ "hex": "#1A2B3C" }` / `{ "rgba": { "r": 26, "g": 43, "b": 60, "a": 1 } }`. Stored rebuilt in the full picker shape. |
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
| `ShowSweeppeaBranding` | Boolean | White-label mode. `true` (default) shows the "Made with Sweeppea" link on the entry page and AMOE form. Setting it to `false` hides the branding and requires an active paid plan (returns `403` otherwise). |
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
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 42,
    "MaxAPICalls": 10000
  },
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
  "Message": "Missing or invalid Bearer token. Send your API token in the Authorization header as: Authorization: Bearer YOUR_API_TOKEN",
  "Code": 401
}
```

**403 Forbidden**

```json
{
  "Response": false,
  "Message": "Invalid API token. It does not match any account.",
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
  "Message": "Internal server error. Please try again; if it persists, contact support with the time of this request.",
  "Code": 500
}
```

## Notes

- **Partial Updates:** Send only the fields you want to change — between 1 and 5 per request.
- **Ownership Verification:** The entry page must exist and belong to the authenticated user.
- **Type Validation:** Every value is strictly validated against its expected type before being saved.
- **Measure Field:** `EntryPageWidthMeasure` only accepts `"%"` or `"px"`.
- **Color Fields:** A hex string or an object with a valid `hexa`, `hex` or `rgba` value. Anything that is not a real color is rejected with `400`; accepted colors are stored rebuilt as `{ alpha, hex, hexa, hsla, hsva, hue, rgba }`.
- **Background Scope:** `EntryPageBackgroundScope` accepts only `"outside"`, `"full"` or `"custom"`. Entry pages without it render as `"outside"`.
- **Help:** Every `400` validation error carries a `Help.ExpectedBody` object listing every accepted field and its format.
- **UUID Fields:** `SelectedOfficialRules` must be a valid UUID v4 (token of an existing rules document).
- **URL Fields:** Sponsor profile fields accept a URL string or `null`.
- **Max 5 Fields:** Requests with more than 5 fields will be rejected.
- **Not Found:** Returns 404 if the entry page doesn't exist or belongs to another user.
