# Fetch Sweepstakes

Fetch all sweepstakes associated with the authenticated user account.

## Endpoint

`POST sweepstakes/fetch`

## Description

This endpoint allows you to fetch all sweepstakes associated with a specific user account. Use this to retrieve comprehensive sweepstakes information programmatically through the Sweeppea API.

## Code Examples

### cURL

```bash
curl -X POST "https://api-v3.sweeppea.com/sweepstakes/fetch" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

### JavaScript

```javascript
const response = await fetch('https://api-v3.sweeppea.com/sweepstakes/fetch', {
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

url = "https://api-v3.sweeppea.com/sweepstakes/fetch"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

response = requests.post(url, headers=headers)
print(response.json())
```

## Response

**200 OK**
```json
{
  "Response": true,
  "Data": [
    {
      "SweepstakesToken": "uuid-v4-string",
      "UserToken": "uuid-v4-string",
      "SweepstakesName": "Summer Vacation Giveaway 2025",
      "SweepstakesType": 2,
      "Handler": "SUMMERVAC2025",
      "Settings": {
        "Language": "en",
        "Timezone": 5,
        "StartDate": "2025-06-01",
        "ExpirationDate": "2025-08-31",
        "StartTime": "00:00",
        "EndTime": "23:59",
        "EmailSenderFrom": "noreply@example.com",
        "EmailSenderSubject": "Welcome to Summer Vacation Giveaway!",
        "PrimaryOptInMessageSMS": "SUMMERVAC2025: Almost done! Complete your entry at https://example.com/enter Send HELP for help. Msgs & Data rates may apply. Reply STOP to end.",
        "PrimaryOptInMessageEmail": "SUMMERVAC2025: Thank you for entering our summer promotion. We wish you the best of luck!",
        "SecondaryOptInMessageSMS": "SUMMERVAC2025: Don't forget to share with friends for bonus entries!",
        "SecondaryOptInMessageEmail": "Share this promotion with your friends and family to increase your chances of winning!",
        "ExpirationMessageSMS": "SUMMERVAC2025: This promotion has ended. Thank you for participating!",
        "ExpirationMessageEmail": "Thank you for participating in our Summer Vacation Giveaway. Stay tuned for future promotions!",
        "AllowDuplicatedEntries": true,
        "MaxAllowedEntriesForParticipant": 5,
        "MaxAllowedParticipants": 10000,
        "DefaultGroup": "uuid-v4-string",
        "InstantWinFrequency": 100,
        "InstantWinMessageSMS": "SUMMERVAC2025: Congratulations! You won a $50 gift card! Contact us at prizes@example.com to claim.",
        "InstantWinMessageEmail": "Congratulations! You are an instant winner! You've won a $50 gift card. Please reply to this email to claim your prize.",
        "OneTimeReminder": true,
        "OneTimeReminderMessageSMS": "SUMMERVAC2025: Reminder! Complete your entry at https://example.com/enter for a chance to win an amazing vacation package!",
        "OneTimeReminderMessageEmail": "Don't miss out! Complete your entry for the Summer Vacation Giveaway and you could win an all-expenses-paid trip!",
        "ReportFrequency": 7,
        "UnsubscribedParticipantsRemainElegible": false,
        "DefaultStopMessage": "SUMMERVAC2025: Opt-out confirmed. You will no longer receive messages. To opt-in again, send SUMMERVAC2025 to (555) 123-4567",
        "SendHintSmsGateway": true,
        "ConfirmationResponseSwitch": true
      },
      "Notes": [
        {
          "Id": "note123456789",
          "CreationDate": "2025-05-15T10:30:00.000Z",
          "CreatedBy": "Marketing Team",
          "Note": "Launch campaign on social media platforms on June 1st."
        },
        {
          "Id": "note987654321",
          "CreationDate": "2025-05-20T14:45:00.000Z",
          "CreatedBy": "Admin User",
          "Note": "Coordinate with prize fulfillment team for instant win prizes."
        }
      ],
      "AdminChecklist": [
        {
          "Task": "Verify prize inventory",
          "Completed": true,
          "CompletedDate": "2025-05-10T09:00:00.000Z"
        },
        {
          "Task": "Test SMS gateway",
          "Completed": true,
          "CompletedDate": "2025-05-12T11:30:00.000Z"
        },
        {
          "Task": "Review legal compliance",
          "Completed": false,
          "CompletedDate": null
        }
      ],
      "Score": 85,
      "Archived": false,
      "Status": true,
      "CreationDate": "2025-05-01T08:00:00.000Z"
    },
    {
      "SweepstakesToken": "uuid-v4-string",
      "UserToken": "uuid-v4-string",
      "SweepstakesName": "Holiday Shopping Spree",
      "SweepstakesType": 1,
      "Handler": "HOLIDAYSHOP",
      "Settings": {
        "Language": "en",
        "Timezone": 8,
        "StartDate": "2025-11-01",
        "ExpirationDate": "2025-12-25",
        "StartTime": "00:00",
        "EndTime": "23:59",
        "EmailSenderFrom": "contests@example.com",
        "EmailSenderSubject": "Enter to Win $5000 Shopping Spree!",
        "PrimaryOptInMessageSMS": "HOLIDAYSHOP: Complete entry at https://example.com/holiday Reply STOP to end.",
        "PrimaryOptInMessageEmail": "Thank you for entering the Holiday Shopping Spree! Good luck!",
        "SecondaryOptInMessageSMS": "",
        "SecondaryOptInMessageEmail": "",
        "ExpirationMessageSMS": "HOLIDAYSHOP: Contest ended. Winners will be announced soon!",
        "ExpirationMessageEmail": "The Holiday Shopping Spree has ended. Winners will be notified by email.",
        "AllowDuplicatedEntries": false,
        "MaxAllowedEntriesForParticipant": 1,
        "MaxAllowedParticipants": 50000,
        "DefaultGroup": "uuid-v4-string",
        "InstantWinFrequency": 0,
        "InstantWinMessageSMS": "",
        "InstantWinMessageEmail": "",
        "OneTimeReminder": false,
        "OneTimeReminderMessageSMS": "",
        "OneTimeReminderMessageEmail": "",
        "ReportFrequency": 0,
        "UnsubscribedParticipantsRemainElegible": true,
        "DefaultStopMessage": "HOLIDAYSHOP: You've been unsubscribed. Text HOLIDAYSHOP to (555) 987-6543 to rejoin.",
        "SendHintSmsGateway": false,
        "ConfirmationResponseSwitch": false
      },
      "Notes": [],
      "AdminChecklist": [],
      "Score": 92,
      "Archived": false,
      "Status": true,
      "CreationDate": "2025-10-01T12:00:00.000Z"
    }
  ],
  "Count": 2
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
