---
name: elastic-email-api
description: >
  Use this skill when integrating with Elastic Email for sending emails,
  managing contacts, lists, campaigns, templates, domains, suppressions,
  segments, or verifications via the Elastic Email REST API v4. Covers direct
  HTTP requests to all API endpoints, official SDK libraries for Python, C#,
  Java, PHP, JavaScript, TypeScript (Angular/Axios), Go, Ruby, Rust, Perl,
  and Bash, as well as the Elastic Email MCP server for AI-powered email
  workflows. Use when user says "send email with Elastic Email", "Elastic Email", "Elastic
  Email API", "email API integration", "manage contacts Elastic Email",
  "email campaign API", "transactional email", "bulk email API", "Elastic
  Email SDK", "Elastic Email MCP", or mentions any Elastic Email endpoint.
license: MIT
metadata:
  author: ElasticEmail
  version: 1.0.0
  api-version: v4
---

# Elastic Email API Development Skill

## Overview

The Elastic Email REST API v4 enables programmatic email sending, contact management, campaign orchestration, template management, domain configuration, and delivery analytics. Key capabilities include:

- **Transactional emails** — Send one-to-one emails triggered by user actions
- **Bulk emails** — Send mass emails to lists of recipients with merge fields
- **Campaign management** — Create, update, pause, and delete email campaigns
- **Contact management** — Add, update, delete, import, and export contacts
- **List management** — Create and manage contact lists and segments
- **Template management** — Create, load, and delete email templates
- **Domain management** — Add, verify, and configure sending domains
- **Suppression management** — Manage bounces, complaints, and unsubscribes
- **Email verification** — Verify email addresses before sending
- **Statistics & events** — Track delivery, opens, clicks, and bounces
- **File management** — Upload and manage attachments
- **Inbound routing** — Configure inbound email processing
- **Security** — Manage API keys and SMTP credentials

## API Base URL

```
https://api.elasticemail.com/v4
```

## Authentication

All API calls require an API key sent in the request header:

```
x-elasticemail-apikey: YOUR_API_KEY
```

Generate your API key at: https://app.elasticemail.com/marketing/settings/new/manage-api

> **Important:** The API has a limit of 20 concurrent connections and a hard timeout of 600 seconds per request. Maximum email size (message + attachments) is 20MB.

## OpenAPI Specification

The full OpenAPI 3.0.3 specification is available for download and can be used with Postman, Insomnia, or any OpenAPI-compatible tool:

```
https://api.elasticemail.com/public/v4/swagger
```

## Official SDK Libraries

Elastic Email provides official client libraries for rapid integration. All libraries are available on GitHub at https://github.com/ElasticEmail

| Language | Package / Repository | Install Command |
|---|---|---|
| **Python** | `ElasticEmail` | `pip install ElasticEmail` or `pip install git+https://github.com/ElasticEmail/elasticemail-python.git` |
| **C#** | `ElasticEmail` (NuGet) | `dotnet add package ElasticEmail` |
| **Java** | `elasticemail-java` | Add Maven/Gradle dependency from GitHub |
| **PHP** | `elasticemail/elasticemail-php` | `composer require elasticemail/elasticemail-php` |
| **JavaScript** | `@elasticemail/elasticemail-client` | `npm install @elasticemail/elasticemail-client` |
| **TypeScript Angular** | `@elasticemail/elasticemail-client` | `npm install @elasticemail/elasticemail-client` |
| **TypeScript Axios** | `@elasticemail/elasticemail-client` | `npm install @elasticemail/elasticemail-client` |
| **Go** | `elasticemail-go` | `go get github.com/ElasticEmail/elasticemail-go` |
| **Ruby** | `ElasticEmail` | `gem install ElasticEmail` |
| **Rust** | `elasticemail` | Add to `Cargo.toml` from GitHub |
| **Perl** | `ElasticEmail::Client` | Install from GitHub |
| **Bash** | Shell scripts | Clone from GitHub |

> **Note:** Always check the respective GitHub repository for the latest version and installation instructions.

## Quick Start — Direct HTTP Requests

### Send a Transactional Email (cURL)

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
      "Subject": "Hello from Elastic Email",
      "Body": [
        {
          "ContentType": "HTML",
          "Content": "<h1>Hello!</h1><p>This is a test email.</p>",
          "Charset": "utf-8"
        }
      ]
    }
  }'
```

### Send Bulk Emails (cURL)

```bash
curl -X POST "https://api.elasticemail.com/v4/emails" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Recipients": {
      "To": ["user1@example.com", "user2@example.com"]
    },
    "Content": {
      "From": "sender@yourdomain.com",
      "Subject": "Bulk Message",
      "Body": [
        {
          "ContentType": "HTML",
          "Content": "<p>Hello {firstname}, this is a bulk email.</p>",
          "Charset": "utf-8"
        }
      ]
    }
  }'
```

## Quick Start — Python SDK

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
            subject="Hello from Elastic Email",
            body=[
                BodyPart(
                    content_type=BodyContentType("HTML"),
                    content="<h1>Hello!</h1><p>This is a test email.</p>",
                    charset="utf-8",
                ),
            ],
        ),
    )

    try:
        response = api_instance.emails_transactional_post(email_data)
        print(response)
    except ElasticEmail.ApiException as e:
        print(f"Error: {e}")
```

## Quick Start — C# SDK

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
        Subject = "Hello from Elastic Email",
        Body = new List<BodyPart>
        {
            new BodyPart(
                contentType: BodyContentType.HTML,
                content: "<h1>Hello!</h1><p>This is a test email.</p>",
                charset: "utf-8"
            )
        }
    }
);

var response = emailsApi.EmailsTransactionalPost(emailData);
Console.WriteLine(response);
```

## Quick Start — JavaScript SDK

```javascript
const ElasticEmail = require('@elasticemail/elasticemail-client');

const client = ElasticEmail.ApiClient.instance;
const apiKey = client.authentications['apikey'];
apiKey.apiKey = 'YOUR_API_KEY';

const emailsApi = new ElasticEmail.EmailsApi();

const emailData = {
  Recipients: {
    To: ['recipient@example.com']
  },
  Content: {
    From: 'sender@yourdomain.com',
    Subject: 'Hello from Elastic Email',
    Body: [
      {
        ContentType: 'HTML',
        Content: '<h1>Hello!</h1><p>This is a test email.</p>',
        Charset: 'utf-8'
      }
    ]
  }
};

emailsApi.emailsTransactionalPost(emailData, (error, data) => {
  if (error) {
    console.error(error);
  } else {
    console.log(data);
  }
});
```

## API Endpoints Reference

For the complete list of all API endpoints with parameters, request/response schemas, and required access levels, consult `references/rest-api-endpoints.md`.

### Endpoint Categories

| Category | Key Endpoints | Description |
|---|---|---|
| **Emails** | `POST /emails`, `POST /emails/transactional`, `POST /emails/mergefile` | Send bulk, transactional, and merge-file emails |
| **Campaigns** | `GET/POST /campaigns`, `PUT/DELETE /campaigns/{name}` | CRUD operations on campaigns |
| **Contacts** | `GET/POST /contacts`, `PUT/DELETE /contacts/{email}` | Manage individual contacts |
| **Lists** | `GET/POST /lists`, `GET/PUT/DELETE /lists/{name}` | Contact list management |
| **Templates** | `GET/POST /templates`, `GET/PUT/DELETE /templates/{name}` | Email template CRUD |
| **Domains** | `GET/POST /domains`, `GET/PUT/DELETE /domains/{domain}` | Domain configuration |
| **Events** | `GET /events`, `GET /events/{transactionid}` | Delivery event tracking |
| **Statistics** | `GET /statistics`, `GET /statistics/campaigns` | Email analytics |
| **Segments** | `GET/POST /segments`, `GET/PUT/DELETE /segments/{name}` | Dynamic contact segments |
| **Suppressions** | `GET/POST/DELETE /suppressions` | Bounces, complaints, unsubscribes |
| **Verifications** | `POST /verifications`, `GET /verifications/{email}` | Email address verification |
| **Files** | `GET/POST /files`, `GET/DELETE /files/{name}` | File/attachment management |
| **Inbound Route** | `GET/POST /inboundroute`, `GET/PUT/DELETE /inboundroute/{id}` | Inbound email routing |
| **Security** | `GET/POST /security/apikeys`, `GET/POST /security/smtp` | API key and SMTP management |
| **SubAccounts** | `GET/POST /subaccounts`, `GET/DELETE /subaccounts/{email}` | Sub-account management |

## SDK Libraries — Detailed Usage

For comprehensive SDK setup, authentication, and code examples in all supported languages, consult `references/sdk-libraries.md`.

### SDK Client Configuration Pattern

All SDK libraries follow the same pattern:

1. **Import** the library
2. **Configure** the API key
3. **Create** an API client instance
4. **Instantiate** the specific API class (EmailsApi, ContactsApi, etc.)
5. **Call** the desired method with required parameters
6. **Handle** the response and errors

## Elastic Email MCP Server

The Elastic Email MCP (Model Context Protocol) server turns AI tools into email agents. It is available as a hosted service at `https://mcp.elasticemail.com` — no local installation required. Alternatively, a self-hosted .NET server is available at https://github.com/ElasticEmail/elasticemail-mcp-server. For detailed MCP tool documentation, consult `references/mcp-tools.md`.

**Documentation:** https://help.elasticemail.com/en/articles/12595879-elastic-email-mcp

### MCP Setup (Hosted)

Add to your MCP client configuration (VS Code, Cursor, or any MCP-compatible client):

```json
{
  "servers": {
    "elasticemail.mcp": {
      "url": "https://mcp.elasticemail.com",
      "headers": {
        "X-Auth-Token": "YOUR_API_KEY"
      }
    }
  }
}
```

### MCP Setup (Self-hosted .NET)

Requires .NET SDK 10+. After building and running the server locally (port 5001):

```json
{
  "servers": {
    "elasticemail.mcp": {
      "url": "http://localhost:5001/",
      "headers": {
        "X-Auth-Token": "YOUR_API_KEY"
      }
    }
  }
}
```

### MCP Tool Categories

| Category | Tools | Description |
|---|---|---|
| **Emails** | `SendTransactionalEmail`, `SendBulkEmails` | Send transactional and bulk emails |
| **Campaigns** | `CreateCampaign`, `ListCampaigns`, `GetCampaign`, `Pause`, `UpdateCampaign` | Campaign lifecycle |
| **Contacts** | `FetchContacts`, `AddContact`, `DeleteContacts`, `UploadContacts`, `FetchContactHistory` | Contact CRUD and history |
| **Lists** | `FetchLists`, `FetchList`, `FetchListContacts`, `CreateList`, `AddContactToList`, `RemoveContactsToList` | List management |
| **Segments** | `CreateSegment`, `GetSegments`, `GetSegment` | Segment operations |
| **Templates** | `FetchTemplates`, `FetchTemplate` | Template retrieval |
| **Statistics** | `GetCampaignStatistics`, `GetAllCampaignStatistics` | Campaign analytics |
| **System** | `IsReady` | MCP connectivity check |

## Common Workflows

### Workflow 1: Send a Transactional Email

1. Ensure your domain is verified (`GET /domains/{domain}`)
2. Optionally load a template (`GET /templates/{name}`)
3. Send the email (`POST /emails/transactional`)
4. Check the status (`GET /emails/{transactionid}/status`)

### Workflow 2: Create and Send a Campaign

1. Create or select a contact list (`POST /lists` or `GET /lists`)
2. Create an email template (`POST /templates`)
3. Create the campaign with list and template references (`POST /campaigns`)
4. Monitor campaign statistics (`GET /statistics/campaigns/{name}`)

### Workflow 3: Import Contacts and Manage Lists

1. Prepare a CSV file with contact data (Email column required)
2. Upload contacts (`POST /contacts/import`)
3. Create or assign to a list (`POST /lists`)
4. Add contacts to the list (`POST /lists/{name}/contacts`)

### Workflow 4: Verify Emails Before Sending

1. Submit emails for verification (`POST /verifications`)
2. Check verification results (`GET /verifications/{email}`)
3. Remove invalid addresses from your list
4. Send to verified addresses only

### Workflow 5: Domain Setup

1. Add your domain (`POST /domains`)
2. Configure DNS records as provided
3. Verify the domain (`PUT /domains/{domain}/verification`)
4. Set as default sender (`PATCH /domains/{email}/default`)

## Error Handling

All API responses follow standard HTTP status codes:

| Code | Meaning |
|---|---|
| `200` | Success |
| `201` | Created |
| `202` | Accepted (async operation) |
| `400` | Bad request — check parameters |
| `401` | Unauthorized — invalid API key |
| `403` | Forbidden — insufficient access level |
| `404` | Not found |
| `409` | Conflict |
| `429` | Rate limited — too many requests |
| `500` | Server error |

### Required Access Levels

Different API endpoints require different access levels on your API key:

- **ViewContacts** — Read contact data
- **ModifyContacts** — Create, update, delete contacts
- **ViewCampaigns** — Read campaign data
- **ModifyCampaigns** — Create, update, delete campaigns
- **SendHttp** — Send emails via API
- **ViewSettings** — Read domain/account settings
- **ModifySettings** — Change domain/account settings
- **ViewTemplates** — Read templates
- **ModifyTemplates** — Create, update, delete templates
- **ViewReports** — Read statistics and event logs
- **Export** — Export contact data

## Troubleshooting

### Authentication Errors
**Error:** `401 Unauthorized`
**Cause:** Invalid or missing API key
**Solution:** Verify your API key in the `x-elasticemail-apikey` header. Generate a new key at https://app.elasticemail.com/marketing/settings/new/manage-api or https://app.elasticemail.com/api/settings/new/manage-api

### Rate Limiting
**Error:** `429 Too Many Requests`
**Cause:** Exceeded 20 concurrent connections
**Solution:** Implement request queuing or exponential backoff. The API allows 20 concurrent connections.

### Domain Not Verified
**Error:** Emails fail to send or get rejected
**Cause:** Sending domain is not verified
**Solution:** Verify your domain via `PUT /domains/{domain}/verification` after configuring DNS records.

### Timeout Errors
**Error:** Request timeout
**Cause:** Request exceeded 600-second limit
**Solution:** Break large operations (e.g., bulk imports) into smaller batches.

### Email Size Exceeded
**Error:** `400 Bad Request` on email send
**Cause:** Email + attachments exceed 20MB
**Solution:** Reduce attachment sizes or use file hosting links instead.

## Best Practices

1. **Always verify your sending domain** before sending any emails
2. **Use transactional endpoint** for triggered/individual emails and bulk endpoint for mass sends
3. **Implement exponential backoff** for rate limit handling
4. **Use templates** for consistent email formatting and merge field support
5. **Monitor suppressions** regularly to maintain sender reputation
6. **Use email verification** before sending to reduce bounces
7. **Set appropriate API key access levels** — use the principle of least privilege
8. **Use the MCP server** for AI-assisted email workflows when available
9. **Keep SDK libraries updated** — check GitHub repositories for latest versions
10. **Use pagination** (`offset` and `limit` parameters) when fetching large datasets
