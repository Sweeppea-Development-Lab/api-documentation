# Fetch Area Codes

Search or list US area codes with their states. Returns up to 10 results.

## Endpoint

`POST /tools/areacodes`

## Description

This endpoint retrieves US area codes. Search by area code or state name, or retrieve all (up to 10). Perfect for phone number validation or area code lookup.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `search` | String | Optional | Search term (area code or state name) |

## Request Example

```json
{
  "search": "305"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/tools/areacodes" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"search": "305"}'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/tools/areacodes', {
  method: 'POST',
  headers: {'Authorization': 'Bearer YOUR_API_KEY', 'Content-Type': 'application/json'},
  body: JSON.stringify({search: '305'})
});
const data = await response.json();
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/tools/areacodes"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {"search": "305"}

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
    "AreaCodes": [
      {"Code": "305", "State": "Florida"}
    ]
  }
}
```

## Notes

- **Optional Search:** Omit `search` to retrieve all area codes (max 10).
- **Maximum Results:** Returns up to 10 results per request.
- **Search Fields:** Searches area code and state name.
