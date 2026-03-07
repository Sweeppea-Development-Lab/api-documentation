# Fetch Countries

Search or list countries with dial codes and abbreviations. Returns up to 10 results.

## Endpoint

`POST /tools/countries`

## Description

This endpoint retrieves countries with names in English and Spanish, dial codes, and abbreviations. Search by any field or retrieve all (up to 10).

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `search` | String | Optional | Search term (name, dial code, or abbreviation) |

## Request Example

```json
{
  "search": "Spain"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tools/countries" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"search": "Spain"}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tools/countries', {
  method: 'POST',
  headers: {'Authorization': 'Bearer YOUR_API_KEY', 'Content-Type': 'application/json'},
  body: JSON.stringify({search: 'Spain'})
});
const data = await response.json();
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/tools/countries"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {"search": "Spain"}

response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Data": {
    "TotalResults": 1,
    "Countries": [
      {
        "NameEnglish": "Spain",
        "NameSpanish": "España",
        "DialCode": "+34",
        "Abbreviation": "ES"
      }
    ]
  }
}
```

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `NameEnglish` | String | Country name in English |
| `NameSpanish` | String | Country name in Spanish |
| `DialCode` | String | International dial code (e.g., "+34") |
| `Abbreviation` | String | ISO country code (e.g., "ES") |

## Notes

- **Optional Search:** Omit `search` to retrieve all countries (max 10).
- **Maximum Results:** Returns up to 10 results per request.
- **Bilingual:** Includes names in both English and Spanish.
- **Search Fields:** Searches across all fields (names, dial code, abbreviation).
