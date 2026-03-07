# Fetch Entry Page Info

Fetch Entry Page Info API endpoint for Sweeppea. Retrieve entry page data for a given sweepstakes programmatically through the Sweeppea API.

## Endpoint

`GET /entrypage/{sweepstakes_id}`

## Path Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sweepstakes_id` | String | Yes | Unique identifier of the sweepstakes |

## Code Examples

### cURL

```bash
curl -X GET "https://api-v3.sweeppea.com/entrypage/{sweepstakes_id}" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/entrypage/{sweepstakes_id}', {
  method: 'GET',
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

url = "https://api-v3.sweeppea.com/entrypage/{sweepstakes_id}"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

response = requests.get(url, headers=headers)
print(response.json())
```

## Response

**200 OK**

```json
{
  "success": true,
  "data": {},
  "message": "Operation completed successfully"
}
```
