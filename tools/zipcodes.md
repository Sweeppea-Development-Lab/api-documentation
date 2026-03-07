# Fetch Zip Codes

Search US zip codes by zip code, city, or state. Returns up to 10 results.

## Endpoint

`POST /tools/zipcodes`

## Description

This endpoint searches US zip codes by zip code, city name, or state abbreviation. The search parameter is required and returns a maximum of 10 results. Perfect for address autocomplete or validation.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `search` | String | Required | Search term (zip code, city, or state) |

## Request Example

```json
{
  "search": "Miami"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tools/zipcodes" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"search": "Miami"}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tools/zipcodes', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({search: 'Miami'})
});
const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/tools/zipcodes"
headers = {"Authorization": "Bearer YOUR_API_KEY", "Content-Type": "application/json"}
payload = {"search": "Miami"}
response = requests.post(url, headers=headers, json=payload)
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Data": {
    "TotalResults": 10,
    "ZipCodes": [
      {"ZipCode": "33101", "City": "Miami", "State": "FL"},
      {"ZipCode": "33102", "City": "Miami", "State": "FL"}
    ]
  }
}
```

**400 Bad Request**
```json
{
  "Response": false,
  "Message": "Search parameter is required",
  "Code": 400
}
```

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `TotalResults` | Number | Number of results returned (max 10) |
| `ZipCode` | String | 5-digit zip code |
| `City` | String | City name |
| `State` | String | Two-letter state abbreviation |

## Notes

- **Required Search:** The `search` parameter is mandatory for all requests.
- **Maximum Results:** Returns up to 10 results per search to optimize performance.
- **Search Fields:** Searches across zip code, city name, and state abbreviation.
- **Case Insensitive:** Search is case-insensitive for better user experience.
- **Use Cases:** Address autocomplete, zip code validation, city/state lookup.
