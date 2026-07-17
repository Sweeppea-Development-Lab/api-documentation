# Update Group

Update the name of an existing group for a sweepstakes. The new name must be unique within the sweepstakes.

## Endpoint

`POST /groups/update`

## Description

This endpoint allows you to update the name of an existing group. The group is identified by its GroupToken and must belong to the specified sweepstakes. The new name must be unique within the sweepstakes.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

> **Parameter Casing:** All request parameters are `PascalCase`. For backward compatibility, `camelCase` equivalents (e.g. `sweepstakesToken`) are also accepted.

| Parameter          | Type             | Required | Description                                                              |
|--------------------|------------------|----------|--------------------------------------------------------------------------|
| `SweepstakesToken` | String (UUID v4) | Yes      | The UUID token of the sweepstakes                                        |
| `GroupToken`       | String (UUID v4) | Yes      | The UUID token of the group to update                                    |
| `GroupName`        | String           | Yes      | The new name for the group (must be unique within the sweepstakes)       |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "GroupToken": "uuid-v4-string",
  "GroupName": "Updated Group Name"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/groups/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "uuid-v4-string",
    "GroupToken": "uuid-v4-string",
    "GroupName": "Updated Group Name"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/groups/update', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "uuid-v4-string",
    GroupToken: "uuid-v4-string",
    GroupName: "Updated Group Name"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/groups/update"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "SweepstakesToken": "uuid-v4-string",
    "GroupToken": "uuid-v4-string",
    "GroupName": "Updated Group Name"
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Data": {
    "GroupToken": "uuid-v4-string",
    "GroupName": "Updated Group Name",
    "SweepstakesToken": "uuid-v4-string",
    "Primary": false,
    "Locked": false,
    "CreationDate": "2026-01-15T13:16:43.576Z"
  },
  "Message": "Group updated successfully"
}
```

**400 Bad Request** (duplicate name)

```json
{
  "Response": false,
  "Message": "Group name already exists for this sweepstakes"
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Group not found"
}
```
