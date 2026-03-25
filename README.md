# Elastic Email API Skill

A comprehensive skill for integrating with the [Elastic Email](https://elasticemail.com) REST API v4. This skill teaches AI assistants how to send emails, manage contacts, orchestrate campaigns, configure domains, and leverage the full power of the Elastic Email platform — using direct API requests, official SDK libraries for 12+ languages, and the Elastic Email MCP server.

## Installation

### Using npx skills CLI

```sh
# List available skills
npx skills add ElasticEmail/skills --list

# Install the skill globally
npx skills add ElasticEmail/skills --skill elastic-email-api --global
```

### Using Context7 skills CLI

```sh
# Interactively browse and install
npx ctx7 skills install /ElasticEmail/skills

# Install directly
npx ctx7 skills install /ElasticEmail/skills elastic-email-api
```

### Manual Installation

1. Clone this repository:
   ```sh
   git clone https://github.com/ElasticEmail/skills.git
   ```
2. Copy the `skills/elastic-email-api` folder to your skills directory
3. For Claude.ai: Upload via **Settings > Capabilities > Skills**
4. For Claude Code: Place in your skills directory

## Skills in this repo

### elastic-email-api

Complete skill for working with the Elastic Email REST API v4. Covers:

- **Direct HTTP requests** to all API endpoints (Emails, Contacts, Campaigns, Lists, Templates, Domains, Segments, Suppressions, Verifications, Events, Statistics, Files, Inbound Routing, Security, SubAccounts)
- **Official SDK libraries** for Python, C#, Java, PHP, JavaScript, TypeScript (Angular & Axios), Go, Ruby, Rust, Perl, and Bash
- **MCP server integration** for AI-powered email workflows
- **Authentication** patterns for API keys, SMTP, and MCP
- **Code examples** for every major operation in multiple languages
- **Best practices** for email deliverability, rate limiting, and error handling

## What's Included

```
elasticemail-skills/
├── README.md                                    # This file
├── LICENSE                                      # MIT License
└── skills/
    └── elastic-email-api/
        ├── SKILL.md                             # Main skill file (required)
        └── references/
            ├── rest-api-endpoints.md            # Complete API endpoint reference
            ├── sdk-libraries.md                 # SDK setup & examples for all languages
            ├── mcp-tools.md                     # MCP server tool reference
            ├── authentication.md                # Auth guide (API keys, SMTP, MCP)
            └── code-examples.md                 # Copy-paste code examples
```

## Prerequisites

To use the Elastic Email API, you need:

1. An [Elastic Email account](https://elasticemail.com/select-product) Email Marketing or Email API (free tier available)
2. An API key (generate at [Settings > Manage API Keys](https://app.elasticemail.com/marketing/settings/new/manage-api))

## Quick Start

### Send an email with cURL

```bash
curl -X POST "https://api.elasticemail.com/v4/emails/transactional" \
  -H "x-elasticemail-apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Recipients": { "To": ["recipient@example.com"] },
    "Content": {
      "From": "sender@yourdomain.com",
      "Subject": "Hello!",
      "Body": [{ "ContentType": "HTML", "Content": "<h1>Hello!</h1>" }]
    }
  }'
```

### Send an email with Python SDK

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
        recipients=TransactionalRecipient(to=["recipient@example.com"]),
        content=EmailContent(
            _from="sender@yourdomain.com",
            subject="Hello!",
            body=[BodyPart(content_type=BodyContentType("HTML"), content="<h1>Hello!</h1>")],
        ),
    )
    response = api_instance.emails_transactional_post(email_data)
    print(response)
```

### Use with MCP (VS Code / Cursor / any MCP-compatible client)

The official Elastic Email MCP server is available as a hosted service — no local installation required. Add to your MCP configuration:

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

A self-hosted .NET MCP server is also available: https://github.com/ElasticEmail/elasticemail-mcp-server

## Supported Languages & Frameworks

| Language | Package | GitHub |
|---|---|---|
| Python | `ElasticEmail` | [elasticemail-python](https://github.com/ElasticEmail/elasticemail-python) |
| C# | `ElasticEmail` (NuGet) | [elasticemail-csharp](https://github.com/ElasticEmail/elasticemail-csharp) |
| Java | `elasticemail-java` | [elasticemail-java](https://github.com/ElasticEmail/elasticemail-java) |
| PHP | `elasticemail/elasticemail-php` | [elasticemail-php](https://github.com/ElasticEmail/elasticemail-php) |
| JavaScript | `@elasticemail/elasticemail-client` | [elasticemail-js](https://github.com/ElasticEmail/elasticemail-js) |
| TypeScript Angular | `@elasticemail/elasticemail-client` | [elasticemail-ts-angular](https://github.com/ElasticEmail/elasticemail-ts-angular) |
| TypeScript Axios | `@elasticemail/elasticemail-client` | [elasticemail-ts-axios](https://github.com/ElasticEmail/elasticemail-ts-axios) |
| Go | `elasticemail-go` | [elasticemail-go](https://github.com/ElasticEmail/elasticemail-go) |
| Ruby | `ElasticEmail` | [elasticemail-ruby](https://github.com/ElasticEmail/elasticemail-ruby) |
| Rust | `elasticemail` | [elasticemail-rust](https://github.com/ElasticEmail/elasticemail-rust) |
| Perl | `ElasticEmail::Client` | [elasticemail-perl](https://github.com/ElasticEmail/elasticemail-perl) |
| Bash | Shell scripts | [elasticemail-bash](https://github.com/ElasticEmail/elasticemail-bash) |

## Resources

- [Elastic Email REST API Documentation](https://elasticemail.com/developers/api-documentation/rest-api)
- [Elastic Email API Libraries](https://elasticemail.com/developers/api-libraries)
- [Elastic Email MCP Documentation](https://help.elasticemail.com/en/articles/12595879-elastic-email-mcp)
- [Elastic Email MCP Server (.NET, self-hosted)](https://github.com/ElasticEmail/elasticemail-mcp-server)
- [OpenAPI Specification (Swagger)](https://api.elasticemail.com/public/v4/swagger)
- [Elastic Email Help Center](https://help.elasticemail.com/en/)
- [Elastic Email GitHub](https://github.com/ElasticEmail)

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
