# Elastic Email SDK Libraries — Complete Reference

Elastic Email provides official client libraries for 12 languages/frameworks. All libraries are auto-generated from the OpenAPI specification and follow the same architectural pattern.

GitHub organization: https://github.com/ElasticEmail

---

## Python

**Repository:** https://github.com/ElasticEmail/elasticemail-python
**Requirements:** Python 3.8+
**Install:**
```bash
pip install ElasticEmail
```

### Configuration
```python
import os
import ElasticEmail

configuration = ElasticEmail.Configuration()
configuration.api_key["apikey"] = os.environ["ELASTICEMAIL_API_KEY"]
```

### Send Transactional Email
```python
from ElasticEmail.models import (
    BodyContentType,
    BodyPart,
    EmailContent,
    EmailTransactionalMessageData,
    TransactionalRecipient,
)

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = ElasticEmail.EmailsApi(api_client)

    email_data = EmailTransactionalMessageData(
        recipients=TransactionalRecipient(to=["recipient@example.com"]),
        content=EmailContent(
            var_from="sender@yourdomain.com",
            subject="Test Email",
            body=[BodyPart(
                content_type=BodyContentType.HTML,
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
from ElasticEmail.models import ContactPayload, ContactStatus

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = ElasticEmail.ContactsApi(api_client)

    contacts = [
        ContactPayload(
            email="john@example.com",
            status=ContactStatus.ACTIVE,
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
from ElasticEmail.models import ListPayload

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = ElasticEmail.ListsApi(api_client)

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
from ElasticEmail.models import BodyContentType, BodyPart, TemplatePayload, TemplateScope

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = ElasticEmail.TemplatesApi(api_client)

    template = TemplatePayload(
        name="Welcome Email",
        subject="Welcome {firstname}!",
        body=[BodyPart(
            content_type=BodyContentType.HTML,
            content="<h1>Welcome {firstname}!</h1>",
            charset="utf-8",
        )],
        template_scope=TemplateScope.PERSONAL,
    )

    try:
        response = api_instance.templates_post(template)
        print(response)
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")
```

### Send Bulk Emails
```python
from ElasticEmail.models import (
    BodyContentType,
    BodyPart,
    EmailContent,
    EmailMessageData,
    EmailRecipient,
)

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = ElasticEmail.EmailsApi(api_client)

    email_data = EmailMessageData(
        recipients=[
            EmailRecipient(email="user1@example.com", fields={"firstname": "Alice"}),
            EmailRecipient(email="user2@example.com", fields={"firstname": "Bob"}),
        ],
        content=EmailContent(
            var_from="sender@yourdomain.com",
            subject="Bulk Message",
            body=[BodyPart(
                content_type=BodyContentType.HTML,
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
from datetime import datetime

with ElasticEmail.ApiClient(configuration) as api_client:
    api_instance = ElasticEmail.StatisticsApi(api_client)

    try:
        response = api_instance.statistics_get(
            var_from=datetime(2025, 1, 1),
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
using System;
using System.Collections.Generic;
using ElasticEmail.Api;
using ElasticEmail.Client;
using ElasticEmail.Model;

var config = new Configuration();
config.ApiKey.Add("X-ElasticEmail-ApiKey", Environment.GetEnvironmentVariable("ELASTICEMAIL_API_KEY"));
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
**Install:** The SDK is not on Maven Central; it is published on [JitPack](https://jitpack.io/#ElasticEmail/elasticemail-java).

**Install (Maven):**
```xml
<repositories>
    <repository>
        <id>jitpack.io</id>
        <url>https://jitpack.io</url>
    </repository>
</repositories>

<dependency>
    <groupId>com.github.ElasticEmail</groupId>
    <artifactId>elasticemail-java</artifactId>
    <version>4.2.0</version>
</dependency>
```

**Install (Gradle):**
```groovy
repositories {
    mavenCentral()
    maven { url 'https://jitpack.io' }
}

dependencies {
    implementation 'com.github.ElasticEmail:elasticemail-java:4.2.0'
}
```

### Send Transactional Email
```java
import com.elasticemail.api.EmailsApi;
import com.elasticemail.client.ApiClient;
import com.elasticemail.client.ApiException;
import com.elasticemail.client.Configuration;
import com.elasticemail.client.auth.ApiKeyAuth;
import com.elasticemail.model.*;

ApiClient apiClient = Configuration.getDefaultApiClient();
ApiKeyAuth apiKey = (ApiKeyAuth) apiClient.getAuthentication("apikey");
apiKey.setApiKey(System.getenv("ELASTICEMAIL_API_KEY"));

EmailsApi emailsApi = new EmailsApi(apiClient);

EmailTransactionalMessageData emailData = new EmailTransactionalMessageData()
    .recipients(new TransactionalRecipient().addToItem("recipient@example.com"))
    .content(new EmailContent()
        .from("sender@yourdomain.com")
        .subject("Test Email")
        .addBodyItem(new BodyPart()
            .contentType(BodyContentType.HTML)
            .content("<h1>Hello!</h1>")
            .charset("utf-8")));

try {
    EmailSend response = emailsApi.emailsTransactionalPost(emailData);
    System.out.println("TransactionID: " + response.getTransactionID());
} catch (ApiException e) {
    System.err.println("Error " + e.getCode() + ": " + e.getResponseBody());
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
    ->setApiKey('X-ElasticEmail-ApiKey', getenv('ELASTICEMAIL_API_KEY'));

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
    echo 'TransactionID: ' . $result->getTransactionId();
} catch (\ElasticEmail\ApiException $e) {
    echo 'Error ' . $e->getCode() . ': ' . $e->getResponseBody();
}
```

---

## JavaScript

**Repository:** https://github.com/ElasticEmail/elasticemail-js
**Requirements:** Node.js (current LTS recommended); browsers via a bundler
**Install:**
```bash
npm install @elasticemail/elasticemail-client
```

### Send Transactional Email
```javascript
const ElasticEmail = require('@elasticemail/elasticemail-client');

const client = ElasticEmail.ApiClient.instance;
client.authentications['apikey'].apiKey = process.env.ELASTICEMAIL_API_KEY;

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
**Requirements:** Angular 19, RxJS ^7.4, TypeScript >=5.5 <5.7
**Install:**
```bash
npm install @elasticemail/elasticemail-client-ts-angular
```

### Configure (server-side providers, e.g. `app.config.server.ts` with Angular SSR)
Never ship the API key in a browser bundle. Also add `provideHttpClient()` to your app providers.
```typescript
import { Configuration } from '@elasticemail/elasticemail-client-ts-angular';

// inside ApplicationConfig.providers
{
    provide: Configuration,
    useFactory: () => new Configuration({
        credentials: { apikey: () => process.env['ELASTICEMAIL_API_KEY'] },
    }),
}
```

### Service Example
```typescript
import { Injectable, inject } from '@angular/core';
import { firstValueFrom } from 'rxjs';
import { BodyContentType, EmailsService } from '@elasticemail/elasticemail-client-ts-angular';

@Injectable({ providedIn: 'root' })
export class EmailService {
    private readonly emailsApi = inject(EmailsService);

    async sendTransactionalEmail(to: string, subject: string, htmlBody: string) {
        return firstValueFrom(this.emailsApi.emailsTransactionalPost({
            Recipients: { To: [to] },
            Content: {
                From: 'sender@yourdomain.com',
                Subject: subject,
                Body: [{ ContentType: BodyContentType.Html, Content: htmlBody, Charset: 'utf-8' }]
            }
        }));
    }
}
```

---

## TypeScript (Axios)

**Repository:** https://github.com/ElasticEmail/elasticemail-ts-axios
**Requirements:** Node.js (current LTS recommended); uses axios 1.x
**Install:**
```bash
npm install @elasticemail/elasticemail-client-ts-axios
```

### Send Transactional Email
```typescript
import { Configuration, EmailsApi, EmailTransactionalMessageData } from '@elasticemail/elasticemail-client-ts-axios';

const config = new Configuration({ apiKey: process.env.ELASTICEMAIL_API_KEY });
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
go get github.com/elasticemail/elasticemail-go/v4@latest
```

The module path ends in `/v4` and the package name is `ElasticEmail`.

### Send Transactional Email
```go
package main

import (
    "context"
    "fmt"
    "os"

    ElasticEmail "github.com/elasticemail/elasticemail-go/v4"
)

func main() {
    client := ElasticEmail.NewAPIClient(ElasticEmail.NewConfiguration())
    ctx := context.WithValue(context.Background(), ElasticEmail.ContextAPIKeys,
        map[string]ElasticEmail.APIKey{
            "apikey": {Key: os.Getenv("ELASTICEMAIL_API_KEY")},
        })

    html := ElasticEmail.NewBodyPart(ElasticEmail.BODYCONTENTTYPE_HTML)
    html.SetContent("<h1>Hello!</h1>")
    html.SetCharset("utf-8")

    content := ElasticEmail.NewEmailContent("sender@yourdomain.com")
    content.SetSubject("Test Email")
    content.SetBody([]ElasticEmail.BodyPart{*html})

    recipients := ElasticEmail.NewTransactionalRecipient([]string{"recipient@example.com"})
    emailData := ElasticEmail.NewEmailTransactionalMessageData(*recipients, *content)

    resp, _, err := client.EmailsAPI.EmailsTransactionalPost(ctx).
        EmailTransactionalMessageData(*emailData).
        Execute()
    if err != nil {
        fmt.Printf("Error: %v\n", err)
        return
    }
    fmt.Println(resp.GetTransactionID())
}
```

---

## Ruby

**Repository:** https://github.com/ElasticEmail/elasticemail-ruby
**Requirements:** Ruby 2.7+
**Install:**
```bash
gem install ElasticEmail
```

### Send Transactional Email
```ruby
require 'ElasticEmail'

ElasticEmail.configure do |config|
  config.api_key['X-ElasticEmail-ApiKey'] = ENV.fetch('ELASTICEMAIL_API_KEY')
end

api_instance = ElasticEmail::EmailsApi.new

email_data = ElasticEmail::EmailTransactionalMessageData.new(
  recipients: ElasticEmail::TransactionalRecipient.new(to: ['recipient@example.com']),
  content: ElasticEmail::EmailContent.new(
    from: 'sender@yourdomain.com',
    subject: 'Test Email',
    body: [ElasticEmail::BodyPart.new(
      content_type: ElasticEmail::BodyContentType::HTML,
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
**Requirements:** Rust 1.75+
**Install:** Add to `Cargo.toml` (the crate is published on crates.io as `ElasticEmail`):
```toml
[dependencies]
ElasticEmail = "4.2"
tokio = { version = "1", features = ["macros", "rt-multi-thread"] }
```

### Send Transactional Email
```rust
use ElasticEmail::apis::configuration::{ApiKey, Configuration};
use ElasticEmail::apis::emails_api;
use ElasticEmail::models::{
    BodyContentType, BodyPart, EmailContent, EmailTransactionalMessageData, TransactionalRecipient,
};

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let config = Configuration {
        api_key: Some(ApiKey {
            prefix: None,
            key: std::env::var("ELASTICEMAIL_API_KEY")?,
        }),
        ..Default::default()
    };

    let content = EmailContent {
        subject: Some("Test Email".to_string()),
        body: Some(vec![BodyPart {
            content: Some("<h1>Hello!</h1>".to_string()),
            charset: Some("utf-8".to_string()),
            ..BodyPart::new(BodyContentType::Html)
        }]),
        ..EmailContent::new("sender@yourdomain.com".to_string())
    };

    let email_data = EmailTransactionalMessageData::new(
        TransactionalRecipient::new(vec!["recipient@example.com".to_string()]),
        content,
    );

    match emails_api::emails_transactional_post(&config, email_data).await {
        Ok(result) => println!("{:?}", result.transaction_id),
        Err(e) => eprintln!("Error: {e}"),
    }
    Ok(())
}
```

---

## Perl

**Repository:** https://github.com/ElasticEmail/elasticemail-perl
**Requirements:** Perl 5.10+
**Install:** Not on CPAN (the `ElasticEmail` CPAN distribution is an unrelated legacy client). Clone and install dependencies:
```bash
git clone https://github.com/ElasticEmail/elasticemail-perl.git
cd elasticemail-perl && cpanm --installdeps .
export PERL5LIB=$PWD/lib:$PERL5LIB
```

### Send Transactional Email
Model constructors take the API field names (`To`, `From`, `ContentType`); results use snake_case accessors.
```perl
use strict;
use warnings;
use ElasticEmail::EmailsApi;
use ElasticEmail::Object::EmailTransactionalMessageData;
use ElasticEmail::Object::TransactionalRecipient;
use ElasticEmail::Object::EmailContent;
use ElasticEmail::Object::BodyPart;

my $emails = ElasticEmail::EmailsApi->new(
    api_key => { 'X-ElasticEmail-ApiKey' => $ENV{ELASTICEMAIL_API_KEY} },
);

my $message = ElasticEmail::Object::EmailTransactionalMessageData->new(
    Recipients => ElasticEmail::Object::TransactionalRecipient->new(
        To => ['recipient@example.com'],
    ),
    Content => ElasticEmail::Object::EmailContent->new(
        From    => 'sender@yourdomain.com',
        Subject => 'Test Email',
        Body    => [
            ElasticEmail::Object::BodyPart->new(
                ContentType => 'HTML',
                Content     => '<h1>Hello!</h1>',
            ),
        ],
    ),
);

my $result = eval {
    $emails->emails_transactional_post(email_transactional_message_data => $message);
};
if ($@) {
    warn "Error: $@";    # e.g. "API Exception(401): Unauthorized ..."
} else {
    print "TransactionID: ", $result->transaction_id, "\n";
}
```

---

## Bash

**Repository:** https://github.com/ElasticEmail/elasticemail-bash
**Requirements:** Bash 4.3+ (macOS's default Bash 3.2 is too old), cURL
**Install:**
```bash
curl -fsSLO https://raw.githubusercontent.com/ElasticEmail/elasticemail-bash/master/ElasticEmail
chmod u+x ElasticEmail
```

### Usage
The Bash client is a single `ElasticEmail` CLI script (not a sourced library). Each API operation is a subcommand named after its operationId (`emailsTransactionalPost`, `contactsGet`, ...). Pass `--host` (without `/v4`) before the operation, and the API key as a `HEADER:VALUE` argument after it. Run `./ElasticEmail <operation> -h` for per-operation help and `--dry-run` to print the cURL command.

```bash
export ELASTICEMAIL_API_KEY="YOUR_API_KEY"

# Send transactional email (JSON body read from stdin via "-")
cat <<'JSON' | ./ElasticEmail --host "https://api.elasticemail.com" --content-type json \
  emailsTransactionalPost - "X-ElasticEmail-ApiKey:$ELASTICEMAIL_API_KEY"
{
  "Recipients": { "To": ["recipient@example.com"] },
  "Content": {
    "From": "sender@yourdomain.com",
    "Subject": "Test",
    "Body": [{ "ContentType": "HTML", "Content": "<h1>Hello!</h1>" }]
  }
}
JSON
```

---

## Common SDK API Classes

The class-based SDKs share the same API classes. Names follow each language's conventions (for example `EmailsAPI` in Go, `ElasticEmail::EmailsApi` in Perl, `EmailsService` in TypeScript Angular). The Bash SDK exposes the same operations as subcommands (for example `emailsTransactionalPost`).

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
| `WebhookApi` | Webhook management for delivery events |
