# Send File by Email

Send a file from the authenticated user's Drive as an email attachment. Uses the Sweeppea email template with a maximum attachment size of 5 MB.

## Endpoint

`POST /files/send`

## Description

This endpoint sends a file from the user's Drive as an email attachment to a specified recipient. The file is downloaded from S3, wrapped in the Sweeppea email template with an optional custom subject and message, and delivered via AWS SES. The maximum attachment size is 5 MB (email providers reject larger attachments). Each share is recorded in the file's `SharedTo` array for tracking purposes.

## Authentication

This endpoint requires Bearer token authentication via the `Authorization` header.

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `FileToken` | String | Yes | UUID v4 of the file to send |
| `RecipientEmail` | String | Yes | Email address of the recipient |
| `EmailSubject` | String | No | Custom email subject (default: `"File shared from Sweeppea"`) |
| `EmailMessage` | String | No | Custom message body to include in the email |

## Request Example

```json
{
  "FileToken": "uuid-v4-string",
  "RecipientEmail": "recipient@example.com",
  "EmailSubject": "Monthly Report",
  "EmailMessage": "Please find attached the monthly report for review."
}
```

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/files/send" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "FileToken": "uuid-v4-string",
    "RecipientEmail": "recipient@example.com",
    "EmailSubject": "Monthly Report",
    "EmailMessage": "Please find attached the monthly report for review."
  }'
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/files/send', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    FileToken: 'uuid-v4-string',
    RecipientEmail: 'recipient@example.com',
    EmailSubject: 'Monthly Report',
    EmailMessage: 'Please find attached the monthly report for review.'
  })
});

const data = await response.json();
console.log(data);
```

### Python

```python
import requests

url = "https://api-v3.sweeppea.com/files/send"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {
    "FileToken": "uuid-v4-string",
    "RecipientEmail": "recipient@example.com",
    "EmailSubject": "Monthly Report",
    "EmailMessage": "Please find attached the monthly report for review."
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
    "DataConsumed": 0.000033,
    "APICalls": 42,
    "MaxAPICalls": 1500000
  },
  "Data": {
    "FileToken": "uuid-v4-string",
    "Filename": "report.pdf",
    "RecipientEmail": "recipient@example.com",
    "SizeMB": 0.03
  },
  "Message": "(OK) File sent by email successfully."
}
```

**400 Bad Request** — Missing or invalid fields

```json
{
  "Response": false,
  "Message": "Invalid or Missing FileToken.",
  "Help": {
    "ExpectedBody": {
      "FileToken": "string (required) — UUID v4 of the file to send",
      "RecipientEmail": "string (required) — Email address of the recipient",
      "EmailSubject": "string (optional) — Custom email subject (default: \"File shared from Sweeppea\")",
      "EmailMessage": "string (optional) — Custom message body to include in the email"
    }
  }
}
```

**400 Bad Request** — File too large for email

```json
{
  "Response": false,
  "Message": "File size (7.50 MB) exceeds the maximum email attachment size of 5 MB. For larger files, download the file directly from your Drive."
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

**404 Not Found** — File not found or access denied

```json
{
  "Response": false,
  "Telemetry": {
    "DataConsumed": 0,
    "APICalls": 42,
    "MaxAPICalls": 1500000
  },
  "Message": "File not found or access denied.",
  "Code": 404
}
```

**500 Internal Server Error**

```json
{
  "Response": false,
  "Message": "Internal Server Error",
  "Code": 500
}
```

## Notes

- **Max Attachment Size:** 5 MB. Files larger than 5 MB are rejected because most email providers bounce larger attachments.
- **Email Template:** The file is sent using the Sweeppea branded email template with the file details (name, size, type) in the body.
- **Custom Content:** Use `EmailSubject` and `EmailMessage` to customize the email. Defaults are provided if omitted.
- **Sharing History:** Each send is recorded in the file's `SharedTo` array with the date, method (`"email"`), and recipient address.
- **Ownership Validation:** Only the file owner can send it. The endpoint verifies that the `FileToken` belongs to the authenticated user.
