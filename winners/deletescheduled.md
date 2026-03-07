# Delete Scheduled Drawing

Delete a pending scheduled winner drawing for a specific sweepstakes.

## Endpoint

`DELETE /winners/deletescheduled`

## Description

This endpoint allows you to delete a scheduled drawing that is in pending status. Only drawings with Status 0 (Pending) can be deleted. Completed or errored drawings cannot be removed.

**Important Notes:**

- The sweepstakes must belong to the authenticated user
- Only pending drawings (Status 0) can be deleted
- Completed or errored drawings cannot be removed
- The scheduled drawing must exist for the specified sweepstakes

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sweepstakesToken` | String (UUID v4) | Required | The unique identifier of the sweepstakes |
| `scheduleToken` | String (UUID v4) | Required | The unique identifier of the scheduled drawing to delete |

## Request Example

```json
{
  "sweepstakesToken": "uuid-v4-string",
  "scheduleToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X DELETE "https://api-v3.sweeppea.com/winners/deletescheduled" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "sweepstakesToken": "uuid-v4-string",
    "scheduleToken": "uuid-v4-string"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/winners/deletescheduled', {
  method: 'DELETE',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    sweepstakesToken: "uuid-v4-string",
    scheduleToken: "uuid-v4-string"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/winners/deletescheduled"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "sweepstakesToken": "uuid-v4-string",
    "scheduleToken": "uuid-v4-string"
}

response = requests.delete(url, headers=headers, json=data)
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Message": "Scheduled drawing deleted successfully"
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
  "Message": "Invalid API token",
  "Code": 403
}
```

**400 Bad Request - Missing Parameters**
```json
{
  "Response": false,
  "Message": "Missing sweepstakesToken or scheduleToken in request body",
  "Code": 400
}
```

**404 Not Found - Sweepstakes**
```json
{
  "Response": false,
  "Message": "Sweepstakes not found",
  "Code": 404
}
```

**403 Forbidden - No Permission**
```json
{
  "Response": false,
  "Message": "You do not have permission to access this sweepstakes",
  "Code": 403
}
```

**404 Not Found - Drawing**
```json
{
  "Response": false,
  "Message": "Scheduled drawing not found",
  "Code": 404
}
```

**400 Bad Request - Cannot Delete**
```json
{
  "Response": false,
  "Message": "Cannot delete scheduled drawing. Only pending drawings can be deleted",
  "Code": 400
}
```
