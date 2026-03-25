# Elastic Email SDK Libraries — Complete Reference

Elastic Email provides official client libraries for 12 languages/frameworks. All libraries are auto-generated from the OpenAPI specification and follow the same architectural pattern.

GitHub organization: https://github.com/ElasticEmail

---

## Python

**Repository:** https://github.com/ElasticEmail/elasticemail-python
**Requirements:** Python 3.6+
**Install:**
```bash
pip install ElasticEmail
# or from source:
pip install git+https://github.com/ElasticEmail/elasticemail-python.git
```

### Configuration
```python
import ElasticEmail

configuration = ElasticEmail.Configuration()
configuration.api_key['apikey'] = 'YOUR_API_KEY'
```

### Send Transactional Email
```python
from ElasticEmail.api import emails_api
from ElasticEmail.model.email_transactional_message_data import EmailTransactionalMessageData
from ElasticEmail.model.transactional_recipient import TransactionalRecipient
from ElasticEmail.model.email_content import EmailContent
from ElasticEmail.model.body_part import BodyPart
from ElasticEmail.model.body_content_type import BodyContentType

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = emails_api.EmailsApi(api_client)

    email_data = EmailTransactionalMessageData(
        recipients=TransactionalRecipient(to=["recipient@example.com"]),
        content=EmailContent(
            _from="sender@yourdomain.com",
            subject="Test Email",
            body=[BodyPart(
                content_type=BodyContentType("HTML"),
                content="<h1>Hello!</h1>",
                charset="utf-8",
            )],
        ),
    )

    try:
        response = api_instance.emails_transactional_post(email_data)
        print(response)
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")
```

### Add Contacts
```python
from ElasticEmail.apis.tags import contacts_api
from ElasticEmail.model.contact_payload import ContactPayload
from ElasticEmail.model.contact_status import ContactStatus

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = contacts_api.ContactsApi(api_client)

    contacts = [
        ContactPayload(
            email="john@example.com",
            status=ContactStatus("Active"),
            first_name="John",
            last_name="Smith",
        ),
    ]

    try:
        response = api_instance.contacts_post(contacts, listnames=["My List"])
        print(response)
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")
```

### Manage Lists
```python
from ElasticEmail.api import lists_api
from ElasticEmail.model.list_payload import ListPayload

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = lists_api.ListsApi(api_client)

    # Create list
    list_payload = ListPayload(
        list_name="My List",
        allow_unsubscribe=True,
        emails=["john@example.com"],
    )

    try:
        response = api_instance.lists_post(list_payload)
        print(response)
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")

    # Load list
    try:
        response = api_instance.lists_by_name_get("My List")
        print(response)
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")

    # Delete list
    try:
        api_instance.lists_by_name_delete("My List")
        print("List deleted.")
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")
```

### Create Template
```python
from ElasticEmail.api import templates_api
from ElasticEmail.model.template_payload import TemplatePayload
from ElasticEmail.model.body_part import BodyPart
from ElasticEmail.model.body_content_type import BodyContentType
from ElasticEmail.model.template_scope import TemplateScope

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = templates_api.TemplatesApi(api_client)

    template = TemplatePayload(
        name="Welcome Email",
        subject="Welcome {firstname}!",
        body=[BodyPart(
            content_type=BodyContentType("HTML"),
            content="<h1>Welcome {firstname}!</h1>",
            charset="utf-8",
        )],
        template_scope=TemplateScope("Personal"),
    )

    try:
        response = api_instance.templates_post(template)
        print(response)
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")
```

### Send Bulk Emails
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
            EmailRecipient(email="user1@example.com"),
            EmailRecipient(email="user2@example.com"),
        ],
        content=EmailContent(
            _from="sender@yourdomain.com",
            subject="Bulk Message",
            body=[BodyPart(
                content_type=BodyContentType("HTML"),
                content="<p>Hello {firstname}!</p>",
                charset="utf-8",
            )],
        ),
    )

    try:
        response = api_instance.emails_post(email_data)
        print(response)
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")
```

### Load Statistics
```python
from ElasticEmail.api import statistics_api
from datetime import datetime

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = statistics_api.StatisticsApi(api_client)

    try:
        response = api_instance.statistics_get(
            _from=datetime(2025, 1, 1),
            to=datetime(2025, 12, 31),
        )
        print(response)
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")
```

---

## C# (.NET)

**Repository:** https://github.com/ElasticEmail/elasticemail-csharp
**Requirements:** .NET Standard 2.0+
**Install:**
```bash
dotnet add package ElasticEmail
# or via NuGet Package Manager:
Install-Package ElasticEmail
```

### Configuration
```csharp
using ElasticEmail.Api;
using ElasticEmail.Client;
using ElasticEmail.Model;

var config = new Configuration();
config.ApiKey.Add("X-ElasticEmail-ApiKey", "YOUR_API_KEY");
```

### Send Transactional Email
```csharp
var emailsApi = new EmailsApi(config);

var emailData = new EmailTransactionalMessageData(
    recipients: new TransactionalRecipient(
        to: new List<string> { "recipient@example.com" }
    ),
    content: new EmailContent
    {
        From = "sender@yourdomain.com",
        Subject = "Test Email",
        Body = new List<BodyPart>
        {
            new BodyPart(BodyContentType.HTML, "<h1>Hello!</h1>", "utf-8")
        }
    }
);

try
{
    var response = emailsApi.EmailsTransactionalPost(emailData);
    Console.WriteLine(response);
}
catch (ApiException e)
{
    Console.WriteLine($"Error: {e.Message}");
}
```

### Add Contacts
```csharp
var contactsApi = new ContactsApi(config);

var contacts = new List<ContactPayload>
{
    new ContactPayload(
        email: "john@example.com",
        status: ContactStatus.Active,
        firstName: "John",
        lastName: "Smith"
    )
};

try
{
    var response = contactsApi.ContactsPost(contacts, listnames: new List<string> { "My List" });
    Console.WriteLine(response);
}
catch (ApiException e)
{
    Console.WriteLine($"Error: {e.Message}");
}
```

---

## Java

**Repository:** https://github.com/ElasticEmail/elasticemail-java
**Requirements:** Java 8+
**Install (Maven):**
```xml
<dependency>
    <groupId>com.elasticemail</groupId>
    <artifactId>elasticemail-java</artifactId>
    <version>LATEST_VERSION</version>
</dependency>
```

**Install (Gradle):**
```groovy
implementation 'com.elasticemail:elasticemail-java:LATEST_VERSION'
```

### Send Transactional Email
```java
import com.elasticemail.client.*;
import com.elasticemail.api.EmailsApi;
import com.elasticemail.model.*;

ApiClient apiClient = Configuration.getDefaultApiClient();
ApiKeyAuth apiKey = (ApiKeyAuth) apiClient.getAuthentication("apikey");
apiKey.setApiKey("YOUR_API_KEY");

EmailsApi emailsApi = new EmailsApi(apiClient);

EmailTransactionalMessageData emailData = new EmailTransactionalMessageData()
    .recipients(new TransactionalRecipient().to(List.of("recipient@example.com")))
    .content(new EmailContent()
        .from("sender@yourdomain.com")
        .subject("Test Email")
        .body(List.of(new BodyPart()
            .contentType(BodyContentType.HTML)
            .content("<h1>Hello!</h1>")
            .charset("utf-8"))));

try {
    EmailSend response = emailsApi.emailsTransactionalPost(emailData);
    System.out.println(response);
} catch (ApiException e) {
    System.err.println("Error: " + e.getMessage());
}
```

---

## PHP

**Repository:** https://github.com/ElasticEmail/elasticemail-php
**Requirements:** PHP 7.4+
**Install:**
```bash
composer require elasticemail/elasticemail-php
```

### Send Transactional Email
```php
<?php
require_once __DIR__ . '/vendor/autoload.php';

$config = ElasticEmail\Configuration::getDefaultConfiguration()
    ->setApiKey('X-ElasticEmail-ApiKey', 'YOUR_API_KEY');

$apiInstance = new ElasticEmail\Api\EmailsApi(
    new GuzzleHttp\Client(),
    $config
);

$emailData = new \ElasticEmail\Model\EmailTransactionalMessageData([
    'recipients' => new \ElasticEmail\Model\TransactionalRecipient([
        'to' => ['recipient@example.com']
    ]),
    'content' => new \ElasticEmail\Model\EmailContent([
        'from' => 'sender@yourdomain.com',
        'subject' => 'Test Email',
        'body' => [
            new \ElasticEmail\Model\BodyPart([
                'content_type' => 'HTML',
                'content' => '<h1>Hello!</h1>',
                'charset' => 'utf-8'
            ])
        ]
    ])
]);

try {
    $result = $apiInstance->emailsTransactionalPost($emailData);
    print_r($result);
} catch (Exception $e) {
    echo 'Error: ' . $e->getMessage();
}
```

---

## JavaScript

**Repository:** https://github.com/ElasticEmail/elasticemail-js
**Requirements:** Node.js 12+
**Install:**
```bash
npm install @elasticemail/elasticemail-client
```

### Send Transactional Email
```javascript
const ElasticEmail = require('@elasticemail/elasticemail-client');

const client = ElasticEmail.ApiClient.instance;
const apiKey = client.authentications['apikey'];
apiKey.apiKey = 'YOUR_API_KEY';

const emailsApi = new ElasticEmail.EmailsApi();

const emailData = ElasticEmail.EmailTransactionalMessageData.constructFromObject({
    Recipients: { To: ['recipient@example.com'] },
    Content: {
        From: 'sender@yourdomain.com',
        Subject: 'Test Email',
        Body: [{
            ContentType: 'HTML',
            Content: '<h1>Hello!</h1>',
            Charset: 'utf-8'
        }]
    }
});

emailsApi.emailsTransactionalPost(emailData, (error, data) => {
    if (error) {
        console.error('Error:', error);
    } else {
        console.log('Success:', data);
    }
});
```

---

## TypeScript (Angular)

**Repository:** https://github.com/ElasticEmail/elasticemail-ts-angular
**Requirements:** Angular 12+
**Install:**
```bash
npm install @elasticemail/elasticemail-client
```

### Service Example
```typescript
import { Injectable } from '@angular/core';
import { EmailsService, Configuration } from '@elasticemail/elasticemail-client';

@Injectable({ providedIn: 'root' })
export class EmailService {
    private emailsApi: EmailsService;

    constructor() {
        const config = new Configuration({ apiKeys: { apikey: 'YOUR_API_KEY' } });
        this.emailsApi = new EmailsService(undefined, undefined, config);
    }

    async sendTransactionalEmail(to: string, subject: string, htmlBody: string) {
        return this.emailsApi.emailsTransactionalPost({
            Recipients: { To: [to] },
            Content: {
                From: 'sender@yourdomain.com',
                Subject: subject,
                Body: [{ ContentType: 'HTML', Content: htmlBody, Charset: 'utf-8' }]
            }
        }).toPromise();
    }
}
```

---

## TypeScript (Axios)

**Repository:** https://github.com/ElasticEmail/elasticemail-ts-axios
**Requirements:** Node.js 12+ with Axios
**Install:**
```bash
npm install @elasticemail/elasticemail-client
```

### Send Transactional Email
```typescript
import { Configuration, EmailsApi, EmailTransactionalMessageData } from '@elasticemail/elasticemail-client';

const config = new Configuration({ apiKey: 'YOUR_API_KEY' });
const emailsApi = new EmailsApi(config);

const emailData: EmailTransactionalMessageData = {
    Recipients: { To: ['recipient@example.com'] },
    Content: {
        From: 'sender@yourdomain.com',
        Subject: 'Test Email',
        Body: [{ ContentType: 'HTML', Content: '<h1>Hello!</h1>', Charset: 'utf-8' }]
    }
};

try {
    const response = await emailsApi.emailsTransactionalPost(emailData);
    console.log(response.data);
} catch (error) {
    console.error('Error:', error);
}
```

---

## Go

**Repository:** https://github.com/ElasticEmail/elasticemail-go
**Requirements:** Go 1.18+
**Install:**
```bash
go get github.com/ElasticEmail/elasticemail-go
```

### Send Transactional Email
```go
package main

import (
    "context"
    "fmt"
    ee "github.com/ElasticEmail/elasticemail-go"
)

func main() {
    cfg := ee.NewConfiguration()
    cfg.AddDefaultHeader("X-ElasticEmail-ApiKey", "YOUR_API_KEY")
    client := ee.NewAPIClient(cfg)

    emailData := *ee.NewEmailTransactionalMessageData(
        *ee.NewTransactionalRecipient([]string{"recipient@example.com"}),
        *ee.NewEmailContent(),
    )
    emailData.Content.SetFrom("sender@yourdomain.com")
    emailData.Content.SetSubject("Test Email")
    emailData.Content.SetBody([]ee.BodyPart{{
        ContentType: ee.BODYCONTENTTYPE_HTML.Ptr(),
        Content:     ee.PtrString("<h1>Hello!</h1>"),
        Charset:     ee.PtrString("utf-8"),
    }})

    resp, _, err := client.EmailsApi.EmailsTransactionalPost(context.Background()).
        EmailTransactionalMessageData(emailData).
        Execute()
    if err != nil {
        fmt.Printf("Error: %v\n", err)
        return
    }
    fmt.Println(resp)
}
```

---

## Ruby

**Repository:** https://github.com/ElasticEmail/elasticemail-ruby
**Requirements:** Ruby 2.5+
**Install:**
```bash
gem install ElasticEmail
```

### Send Transactional Email
```ruby
require 'ElasticEmail'

ElasticEmail.configure do |config|
  config.api_key['apikey'] = 'YOUR_API_KEY'
end

api_instance = ElasticEmail::EmailsApi.new

email_data = ElasticEmail::EmailTransactionalMessageData.new(
  recipients: ElasticEmail::TransactionalRecipient.new(to: ['recipient@example.com']),
  content: ElasticEmail::EmailContent.new(
    from: 'sender@yourdomain.com',
    subject: 'Test Email',
    body: [ElasticEmail::BodyPart.new(
      content_type: 'HTML',
      content: '<h1>Hello!</h1>',
      charset: 'utf-8'
    )]
  )
)

begin
  result = api_instance.emails_transactional_post(email_data)
  puts result
rescue ElasticEmail::ApiError => e
  puts "Error: #{e}"
end
```

---

## Rust

**Repository:** https://github.com/ElasticEmail/elasticemail-rust
**Requirements:** Rust 1.56+
**Install:** Add to `Cargo.toml`:
```toml
[dependencies]
elasticemail = { git = "https://github.com/ElasticEmail/elasticemail-rust" }
```

### Usage
Refer to the GitHub repository README for complete Rust API client usage examples, as the library follows the standard OpenAPI-generated pattern with async/await support.

---

## Perl

**Repository:** https://github.com/ElasticEmail/elasticemail-perl
**Requirements:** Perl 5.26+
**Install:** Clone from GitHub and follow the repository instructions.

### Usage
Refer to the GitHub repository README for complete Perl API client usage examples.

---

## Bash

**Repository:** https://github.com/ElasticEmail/elasticemail-bash
**Requirements:** Bash 4.0+, cURL
**Install:** Clone from GitHub.

### Usage
The Bash client provides shell scripts for all API endpoints. Each endpoint has a corresponding function that can be called with the required parameters.

```bash
# Source the client
source elasticemail-client.sh

# Configure API key
export ELASTIC_EMAIL_API_KEY="YOUR_API_KEY"

# Send transactional email
EmailsTransactionalPost \
    --header "x-elasticemail-apikey: $ELASTIC_EMAIL_API_KEY" \
    --body '{"Recipients":{"To":["recipient@example.com"]},"Content":{"From":"sender@yourdomain.com","Subject":"Test","Body":[{"ContentType":"HTML","Content":"<h1>Hello!</h1>"}]}}'
```

---

## Common SDK API Classes

All SDK libraries provide the following API classes:

| API Class | Description |
|---|---|
| `CampaignsApi` | Campaign CRUD and pause operations |
| `ContactsApi` | Contact management, import, export |
| `DomainsApi` | Domain configuration and verification |
| `EmailsApi` | Send transactional, bulk, and merge emails |
| `EventsApi` | Delivery event retrieval |
| `FilesApi` | File upload and management |
| `InboundRouteApi` | Inbound email routing configuration |
| `ListsApi` | Contact list management |
| `SecurityApi` | API key and SMTP credential management |
| `SegmentsApi` | Dynamic segment CRUD |
| `StatisticsApi` | Email analytics and reporting |
| `SubAccountsApi` | Sub-account management |
| `SuppressionsApi` | Bounce, complaint, and unsubscribe management |
| `TemplatesApi` | Email template CRUD |
| `VerificationsApi` | Email address verification |
