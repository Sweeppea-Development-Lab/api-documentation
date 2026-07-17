# Create Group

Create a new group for a sweepstakes. Groups allow you to organize and segment participants within your sweepstakes.

## Endpoint

`POST /groups/create`

## Description

This endpoint allows you to create a new group for a specific sweepstakes. The group will be associated with the authenticated user and the specified sweepstakes. Use this to manage your participants & groups data programmatically through the Sweeppea API.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

> **Parameter Casing:** All request parameters are `PascalCase`. For backward compatibility, `camelCase` equivalents (e.g. `sweepstakesToken`) are also accepted.

| Parameter          | Type             | Required | Description                                                             |
|--------------------|------------------|----------|-------------------------------------------------------------------------|
| `SweepstakesToken` | String (UUID v4) | Yes      | The UUID token of the sweepstakes where the group will be created       |
| `GroupName`        | String           | Yes      | The name of the group (must be unique within the sweepstakes)           |

## Request Example

```json
{
  "SweepstakesToken": "uuid-v4-string",
  "GroupName": "VIP Members"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/groups/create" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "SweepstakesToken": "uuid-v4-string",
    "GroupName": "VIP Members"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/groups/create', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    SweepstakesToken: "uuid-v4-string",
    GroupName: "VIP Members"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/groups/create"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "SweepstakesToken": "uuid-v4-string",
    "GroupName": "VIP Members"
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
    "GroupName": "VIP Members",
    "SweepstakesToken": "uuid-v4-string",
    "Primary": false,
    "Locked": false,
    "CreationDate": "2026-01-15T13:13:42.851Z"
  },
  "Message": "Group created successfully"
}
```

**400 Bad Request** (missing parameters)

```json
{
  "Response": false,
  "Message": "Missing required parameters: SweepstakesToken and GroupName"
}
```

**400 Bad Request** (duplicate name)

```json
{
  "Response": false,
  "Message": "Group name already exists for this sweepstakes"
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
  "Message": "Invalid API token"
}
```

**404 Not Found**

```json
{
  "Response": false,
  "Message": "Sweepstakes not found or does not belong to user"
}
```
