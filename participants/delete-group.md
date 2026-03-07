# Delete Group

Delete an existing group from a sweepstakes. Groups can only be deleted if they are not primary, not locked, and have no associated participants, AMOE entries, or optouts.

## Endpoint

`POST /groups/delete`

## Description

This endpoint allows you to delete a group from a specific sweepstakes. The group must not be primary, must not be locked, and must not have any participants, AMOE entries, or optouts associated with it. Use this to manage your participants & groups data programmatically through the Sweeppea API.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter          | Type             | Required | Description                                                   |
|--------------------|------------------|----------|---------------------------------------------------------------|
| `sweepstakesToken` | String (UUID v4) | Yes      | The UUID token of the sweepstakes where the group belongs     |
| `groupToken`       | String (UUID v4) | Yes      | The UUID token of the group to be deleted                     |

## Request Example

```json
{
  "sweepstakesToken": "uuid-v4-string",
  "groupToken": "uuid-v4-string"
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/groups/delete" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "sweepstakesToken": "uuid-v4-string",
    "groupToken": "uuid-v4-string"
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/groups/delete', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    sweepstakesToken: "uuid-v4-string",
    groupToken: "uuid-v4-string"
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/groups/delete"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
data = {
    "sweepstakesToken": "uuid-v4-string",
    "groupToken": "uuid-v4-string"
}

response = requests.post(url, headers=headers, json=data)
print(response.json())
```

## Response

**200 OK**

```json
{
  "Response": true,
  "Message": "Group deleted successfully"
}
```

**400 Bad Request** (missing parameters)

```json
{
  "Response": false,
  "Message": "Missing required parameters: sweepstakesToken and groupToken"
}
```

**400 Bad Request** (primary group)

```json
{
  "Response": false,
  "Message": "Cannot delete primary group"
}
```

**400 Bad Request** (locked group)

```json
{
  "Response": false,
  "Message": "Cannot delete locked group"
}
```

**400 Bad Request** (has participants)

```json
{
  "Response": false,
  "Message": "Cannot delete group with participants"
}
```

**400 Bad Request** (has AMOE entries)

```json
{
  "Response": false,
  "Message": "Cannot delete group with AMOE entries"
}
```

**400 Bad Request** (has optouts)

```json
{
  "Response": false,
  "Message": "Cannot delete group with optouts"
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

**404 Not Found** (sweepstakes)

```json
{
  "Response": false,
  "Message": "Sweepstakes not found or does not belong to user"
}
```

**404 Not Found** (group)

```json
{
  "Response": false,
  "Message": "Group not found or does not belong to user"
}
```
