# Elastic Email API — Code Examples

Complete, copy-paste-ready examples for the most common email operations using both direct HTTP requests (cURL) and official SDK libraries.

---

## 1. Send a Transactional Email

### cURL
```bash
curl -X POST "https://api.elasticemail.com/v4/emails/transactional" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Recipients": {
      "To": ["recipient@example.com"]
    },
    "Content": {
      "From": "sender@yourdomain.com",
      "ReplyTo": "reply@yourdomain.com",
      "Subject": "Order Confirmation #12345",
      "Body": [
        {
          "ContentType": "HTML",
          "Content": "<h1>Thank you for your order!</h1><p>Order #12345 has been confirmed.</p>",
          "Charset": "utf-8"
        },
        {
          "ContentType": "PlainText",
          "Content": "Thank you for your order! Order #12345 has been confirmed.",
          "Charset": "utf-8"
        }
      ]
    },
    "Options": {
      "Channel": "OrderConfirmations"
    }
  }'
```

### Python
```python
import ElasticEmail
from ElasticEmail.api import emails_api
from ElasticEmail.model.email_transactional_message_data import EmailTransactionalMessageData
from ElasticEmail.model.transactional_recipient import TransactionalRecipient
from ElasticEmail.model.email_content import EmailContent
from ElasticEmail.model.body_part import BodyPart
from ElasticEmail.model.body_content_type import BodyContentType

configuration = ElasticEmail.Configuration()
configuration.api_key['apikey'] = 'YOUR_API_KEY'

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = emails_api.EmailsApi(api_client)

    email_data = EmailTransactionalMessageData(
        recipients=TransactionalRecipient(
            to=["recipient@example.com"],
        ),
        content=EmailContent(
            _from="sender@yourdomain.com",
            reply_to="reply@yourdomain.com",
            subject="Order Confirmation #12345",
            body=[
                BodyPart(
                    content_type=BodyContentType("HTML"),
                    content="<h1>Thank you!</h1><p>Order #12345 confirmed.</p>",
                    charset="utf-8",
                ),
                BodyPart(
                    content_type=BodyContentType("PlainText"),
                    content="Thank you! Order #12345 confirmed.",
                    charset="utf-8",
                ),
            ],
        ),
    )

    try:
        response = api_instance.emails_transactional_post(email_data)
        print(f"TransactionID: {response.transaction_id}")
        print(f"MessageID: {response.message_id}")
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")
```

### C#
```csharp
using ElasticEmail.Api;
using ElasticEmail.Client;
using ElasticEmail.Model;

var config = new Configuration();
config.ApiKey.Add("X-ElasticEmail-ApiKey", "YOUR_API_KEY");

var emailsApi = new EmailsApi(config);

var emailData = new EmailTransactionalMessageData(
    recipients: new TransactionalRecipient(
        to: new List<string> { "recipient@example.com" }
    ),
    content: new EmailContent
    {
        From = "sender@yourdomain.com",
        ReplyTo = "reply@yourdomain.com",
        Subject = "Order Confirmation #12345",
        Body = new List<BodyPart>
        {
            new BodyPart(BodyContentType.HTML,
                "<h1>Thank you!</h1><p>Order #12345 confirmed.</p>", "utf-8"),
            new BodyPart(BodyContentType.PlainText,
                "Thank you! Order #12345 confirmed.", "utf-8")
        }
    }
);

try
{
    var response = emailsApi.EmailsTransactionalPost(emailData);
    Console.WriteLine($"TransactionID: {response.TransactionID}");
}
catch (ApiException e)
{
    Console.WriteLine($"Error: {e.Message}");
}
```

### PHP
```php
<?php
require_once __DIR__ . '/vendor/autoload.php';

$config = ElasticEmail\Configuration::getDefaultConfiguration()
    ->setApiKey('X-ElasticEmail-ApiKey', 'YOUR_API_KEY');

$apiInstance = new ElasticEmail\Api\EmailsApi(new GuzzleHttp\Client(), $config);

$emailData = new \ElasticEmail\Model\EmailTransactionalMessageData([
    'recipients' => new \ElasticEmail\Model\TransactionalRecipient([
        'to' => ['recipient@example.com']
    ]),
    'content' => new \ElasticEmail\Model\EmailContent([
        'from' => 'sender@yourdomain.com',
        'subject' => 'Order Confirmation #12345',
        'body' => [
            new \ElasticEmail\Model\BodyPart([
                'content_type' => 'HTML',
                'content' => '<h1>Thank you!</h1><p>Order #12345 confirmed.</p>',
                'charset' => 'utf-8'
            ])
        ]
    ])
]);

try {
    $result = $apiInstance->emailsTransactionalPost($emailData);
    echo "TransactionID: " . $result->getTransactionId();
} catch (Exception $e) {
    echo "Error: " . $e->getMessage();
}
```

---

## 2. Send Bulk Emails

### cURL
```bash
curl -X POST "https://api.elasticemail.com/v4/emails" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Recipients": {
      "To": ["user1@example.com", "user2@example.com", "user3@example.com"]
    },
    "Content": {
      "From": "newsletter@yourdomain.com",
      "Subject": "Weekly Newsletter - {date}",
      "Body": [
        {
          "ContentType": "HTML",
          "Content": "<h1>Hello {firstname}!</h1><p>Here is your weekly update.</p>",
          "Charset": "utf-8"
        }
      ]
    },
    "Options": {
      "Channel": "WeeklyNewsletter"
    }
  }'
```

### Python
```python
from ElasticEmail.api import emails_api
from ElasticEmail.model.email_message_data import EmailMessageData
from ElasticEmail.model.email_recipient import EmailRecipient
from ElasticEmail.model.email_content import EmailContent
from ElasticEmail.model.body_part import BodyPart
from ElasticEmail.model.body_content_type import BodyContentType

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = emails_api.EmailsApi(api_client)

    email_data = EmailMessageData(
        recipients=[
            EmailRecipient(email="user1@example.com", fields={"firstname": "Alice"}),
            EmailRecipient(email="user2@example.com", fields={"firstname": "Bob"}),
        ],
        content=EmailContent(
            _from="newsletter@yourdomain.com",
            subject="Weekly Newsletter",
            body=[BodyPart(
                content_type=BodyContentType("HTML"),
                content="<h1>Hello {firstname}!</h1><p>Your weekly update.</p>",
                charset="utf-8",
            )],
        ),
    )

    try:
        response = api_instance.emails_post(email_data)
        print(f"TransactionID: {response.transaction_id}")
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")
```

---

## 3. Manage Contacts

### Add Contacts (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/contacts?listnames=Newsletter" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '[
    {
      "Email": "john@example.com",
      "Status": "Active",
      "FirstName": "John",
      "LastName": "Smith"
    },
    {
      "Email": "jane@example.com",
      "Status": "Active",
      "FirstName": "Jane",
      "LastName": "Doe"
    }
  ]'
```

### Get Contact (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/contacts/john@example.com" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Update Contact (cURL)
```bash
curl -X PUT "https://api.elasticemail.com/v4/contacts/john@example.com" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "FirstName": "Jonathan",
    "CustomFields": {
      "company": "Acme Inc"
    }
  }'
```

### Delete Contact (cURL)
```bash
curl -X DELETE "https://api.elasticemail.com/v4/contacts/john@example.com" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Bulk Delete Contacts (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/contacts/delete" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Emails": ["user1@example.com", "user2@example.com"]
  }'
```

### Import Contacts from CSV (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/contacts/import?listName=Imported&encodingName=utf-8" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -F "file=@contacts.csv"
```

CSV format:
```csv
Email,FirstName,LastName,Status
john@example.com,John,Smith,Active
jane@example.com,Jane,Doe,Active
```

### Export Contacts (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/contacts/export?fileFormat=Csv&compressionFormat=None&fileName=export.csv" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

---

## 4. Manage Lists

### Create List (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/lists" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "ListName": "VIP Customers",
    "AllowUnsubscribe": true,
    "Emails": ["vip1@example.com", "vip2@example.com"]
  }'
```

### Get All Lists (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/lists" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Add Contacts to List (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/lists/VIP%20Customers/contacts" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Emails": ["newvip@example.com"]
  }'
```

### Delete List (cURL)
```bash
curl -X DELETE "https://api.elasticemail.com/v4/lists/VIP%20Customers" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

---

## 5. Manage Templates

### Create Template (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/templates" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Name": "Welcome Email",
    "Subject": "Welcome to {company}!",
    "Body": [
      {
        "ContentType": "HTML",
        "Content": "<h1>Welcome, {firstname}!</h1><p>Thanks for joining {company}.</p>",
        "Charset": "utf-8"
      }
    ],
    "TemplateScope": "Personal"
  }'
```

### Load Template (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/templates/Welcome%20Email" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Send Email with Template (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/emails/transactional" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Recipients": {
      "To": ["newuser@example.com"]
    },
    "Content": {
      "From": "welcome@yourdomain.com",
      "TemplateName": "Welcome Email",
      "Merge": {
        "firstname": "Alice",
        "company": "Acme Inc"
      }
    }
  }'
```

---

## 6. Campaign Operations

### Create Campaign (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/campaigns" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Name": "Spring Sale 2025",
    "Recipients": {
      "ListNames": ["VIP Customers"],
      "SegmentNames": []
    },
    "Content": [{
      "From": "marketing@yourdomain.com",
      "ReplyTo": "support@yourdomain.com",
      "Subject": "Exclusive Spring Sale!",
      "TemplateName": "Spring Sale Template",
      "Merge": {
        "discount": "25%"
      }
    }],
    "Status": "Active"
  }'
```

### List Campaigns (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/campaigns?limit=50&offset=0" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Pause Campaign (cURL)
```bash
curl -X PUT "https://api.elasticemail.com/v4/campaigns/Spring%20Sale%202025/pause" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Delete Campaign (cURL)
```bash
curl -X DELETE "https://api.elasticemail.com/v4/campaigns/Spring%20Sale%202025" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

---

## 7. Domain Management

### Add Domain (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/domains" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Domain": "yourdomain.com"
  }'
```

### Verify Domain (cURL)
```bash
curl -X PUT "https://api.elasticemail.com/v4/domains/yourdomain.com/verification" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '"Http"'
```

### Set Default Domain (cURL)
```bash
curl -X PATCH "https://api.elasticemail.com/v4/domains/sender@yourdomain.com/default" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### List Domains (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/domains" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

---

## 8. Statistics and Tracking

### Get Overall Statistics (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/statistics?from=2025-01-01T00:00:00" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Get Campaign Statistics (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/statistics/campaigns/Spring%20Sale%202025" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Check Email Transaction Status (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/emails/TRANSACTION_ID/status?showDelivered=true&showOpened=true&showClicked=true&showFailed=true" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Get Delivery Events (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/events?from=2025-01-01T00:00:00&limit=100" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

---

## 9. Suppressions

### Get All Suppressions (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/suppressions?limit=100" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Add Bounce Suppression (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/suppressions/bounces" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '["bounced@example.com"]'
```

### Remove from Suppression (cURL)
```bash
curl -X DELETE "https://api.elasticemail.com/v4/suppressions/bounces?email=bounced@example.com" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

---

## 10. Email Verification

### Verify Single Email (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/verifications" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '"test@example.com"'
```

### Bulk Verify from File (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/verifications/files" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -F "file=@emails_to_verify.csv"
```

### Get Verification Result (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/verifications/test@example.com" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

---

## 11. File Management

### Upload File (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/files?expiresAfterDays=30" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -F "file=@attachment.pdf"
```

### List Files (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/files" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Delete File (cURL)
```bash
curl -X DELETE "https://api.elasticemail.com/v4/files/attachment.pdf" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

---

## 12. Security — API Key Management

### Create API Key (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/security/apikeys" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Name": "Production Key",
    "AccessLevel": ["SendHttp", "ViewReports"]
  }'
```

### List API Keys (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/security/apikeys" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

### Delete API Key (cURL)
```bash
curl -X DELETE "https://api.elasticemail.com/v4/security/apikeys/Production%20Key" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

---

## 13. Segments

### Create Segment (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/segments" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Name": "Engaged Users",
    "Rule": "Status = Engaged"
  }'
```

### List Segments (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/segments" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```

---

## 14. Inbound Routing

### Create Inbound Route (cURL)
```bash
curl -X POST "https://api.elasticemail.com/v4/inboundroute" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Filter": "support@yourdomain.com",
    "Name": "Support Inbox",
    "FilterType": "EmailAddress",
    "ActionType": "ForwardToEmail",
    "EmailAddress": "support-team@yourdomain.com"
  }'
```

### List Inbound Routes (cURL)
```bash
curl -X GET "https://api.elasticemail.com/v4/inboundroute" \
  -H "x-elasticemail-apikey: YOUR_API_KEY"
```
