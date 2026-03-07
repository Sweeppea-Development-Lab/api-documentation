# Fetch US States

Retrieve all US states with their abbreviations.

## Endpoint

`POST /tools/states`

## Description

This endpoint retrieves all US states. Each state includes its name and two-letter abbreviation. Useful for displaying state selectors in forms or validating addresses.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tools/states" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tools/states', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  }
});
const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/tools/states"
headers = {"Authorization": "Bearer YOUR_API_KEY", "Content-Type": "application/json"}
response = requests.post(url, headers=headers)
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Data": {
    "TotalStates": 53,
    "States": [
      {"Name": "Alabama", "Abbreviation": "AL"},
      {"Name": "Alaska", "Abbreviation": "AK"}
    ]
  }
}
```

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `TotalStates` | Number | Total number of states available |
| `Name` | String | Full state name |
| `Abbreviation` | String | Two-letter state abbreviation |

## Notes

- **Sorting:** States are sorted alphabetically by name.
- **Total Count:** Includes all 50 US states plus DC, Puerto Rico, and US territories.
- **Abbreviations:** All abbreviations follow the standard USPS two-letter format.
- **Use Cases:** Perfect for address forms, shipping calculators, and location selectors.
