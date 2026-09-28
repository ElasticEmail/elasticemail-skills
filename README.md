<div align="center">

<img src=".github/ee-logo.png" alt="Elastic Email" width="96" />

# Elastic Email Agent Skills

Official agent skills that teach Claude and other AI coding assistants to work with the [Elastic Email](https://elasticemail.com) REST API v4.

[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-SKILL.md-D97757?logo=claude&logoColor=white)](https://agentskills.io)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-ready-D97757?logo=claude&logoColor=white)](https://docs.claude.com/en/docs/claude-code/skills)
[![MCP](https://img.shields.io/badge/MCP-hosted%20server-111111?logo=modelcontextprotocol&logoColor=white)](https://help.elasticemail.com/en/articles/12595879-elastic-email-mcp)
[![API](https://img.shields.io/badge/API-v4-0A7BBB)](https://elasticemail.com/developers/api-documentation/rest-api)
[![SDKs](https://img.shields.io/badge/SDKs-12%20languages-0A7BBB)](#supported-languages)
[![License: MIT](https://img.shields.io/github/license/ElasticEmail/elasticemail-skills?color=yellow)](LICENSE)

[![Latest release](https://img.shields.io/github/v/release/ElasticEmail/elasticemail-skills?logo=github&label=release)](https://github.com/ElasticEmail/elasticemail-skills/releases)
[![Last commit](https://img.shields.io/github/last-commit/ElasticEmail/elasticemail-skills?logo=github)](https://github.com/ElasticEmail/elasticemail-skills/commits/master)
[![Open issues](https://img.shields.io/github/issues/ElasticEmail/elasticemail-skills?logo=github)](https://github.com/ElasticEmail/elasticemail-skills/issues)
[![GitHub stars](https://img.shields.io/github/stars/ElasticEmail/elasticemail-skills?style=flat&logo=github)](https://github.com/ElasticEmail/elasticemail-skills/stargazers)

[Installation](#installation) •
[Quick start](#quick-start) •
[Skills](#skills) •
[Examples](#more-examples) •
[Languages](#supported-languages) •
[Contributing](#contributing)

</div>

---

## Features

- **Transactional and bulk email.** Your assistant knows the send endpoints, payload shape, merge fields and templates.
- **Contacts, lists and segments.** Add, update, import, export and delete contacts, and build lists and segments.
- **Campaigns and templates.** Create, update, pause and monitor campaigns and manage templates.
- **Domains and deliverability.** Walk through domain verification, SPF/DKIM setup, suppressions and email verification.
- **Official SDKs.** Setup and examples for all 12 Elastic Email client libraries, from Python and C# to Rust and Bash.
- **MCP server.** Connect the hosted Elastic Email MCP server and use its tools for AI-driven email workflows.
- **Guardrails built in.** API limits, access levels, error codes and troubleshooting, so generated code handles failures.

## Requirements

| Requirement | Details |
| --- | --- |
| AI assistant | Any client that supports [Agent Skills](https://agentskills.io) (`SKILL.md`): Claude Code, Claude.ai, Claude Desktop and others |
| Elastic Email account | [Email API or Email Marketing](https://elasticemail.com/select-product), free tier available |
| API key | Create one in [Settings → Manage API Keys](https://app.elasticemail.com/marketing/settings/new/manage-api) |

## Installation

Install with the [`skills`](https://www.npmjs.com/package/skills) CLI:

```bash
# List the skills in this repository
npx skills add ElasticEmail/elasticemail-skills --list

# Install the Elastic Email API skill globally
npx skills add ElasticEmail/elasticemail-skills --skill elastic-email-api --global
```

<details>
<summary><b>Other ways to install</b></summary>

**Context7 CLI**

```bash
# Browse and install interactively
npx ctx7 skills install /ElasticEmail/elasticemail-skills

# Install the skill directly
npx ctx7 skills install /ElasticEmail/elasticemail-skills elastic-email-api
```

**Claude Code (manual)**

```bash
git clone https://github.com/ElasticEmail/elasticemail-skills.git
cp -r elasticemail-skills/skills/elastic-email-api ~/.claude/skills/
```

Use `.claude/skills/` inside a project instead of `~/.claude/skills/` to share the skill with your team through the repository.

**Claude.ai and Claude Desktop**

Zip the `skills/elastic-email-api` folder and upload it in **Settings → Capabilities → Skills**.

</details>

## Quick start

Once the skill is installed, ask your assistant in plain language. It loads the skill automatically when a request mentions Elastic Email:

```text
Send a welcome email with Elastic Email from my Express signup handler.
Import contacts.csv into a new Elastic Email list called "Beta users".
Use the Elastic Email Python SDK to send the "order-shipped" template with merge fields.
Why am I getting 403 from the Elastic Email API when I create a campaign?
```

> [!TIP]
> Keep your API key out of source code and prompts. Store it in an environment variable such as `ELASTICEMAIL_API_KEY`, and generated code will read it from there.

### What the skill produces

A request like "send a transactional email with cURL" produces a call like this:

```bash
curl -X POST "https://api.elasticemail.com/v4/emails/transactional" \
  -H "X-ElasticEmail-ApiKey: $ELASTICEMAIL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "Recipients": { "To": ["john.doe@example.com"] },
    "Content": {
      "From": "My App <no-reply@yourdomain.com>",
      "Subject": "Welcome aboard!",
      "Body": [{ "ContentType": "HTML", "Content": "<h1>Hello!</h1>" }]
    }
  }'
```

The same request in Python with the [official SDK](https://github.com/ElasticEmail/elasticemail-python):

```python
import os

import ElasticEmail
from ElasticEmail.models import (
    BodyContentType,
    BodyPart,
    EmailContent,
    EmailTransactionalMessageData,
    TransactionalRecipient,
)

configuration = ElasticEmail.Configuration()
configuration.api_key["apikey"] = os.environ["ELASTICEMAIL_API_KEY"]

message = EmailTransactionalMessageData(
    recipients=TransactionalRecipient(to=["john.doe@example.com"]),
    content=EmailContent(
        var_from="My App <no-reply@yourdomain.com>",
        subject="Welcome aboard!",
        body=[BodyPart(content_type=BodyContentType.HTML, content="<h1>Hello!</h1>")],
    ),
)

with ElasticEmail.ApiClient(configuration) as api_client:
    result = ElasticEmail.EmailsApi(api_client).emails_transactional_post(message)
    print(result.transaction_id)
```

### Connect the MCP server

The skill also covers the hosted [Elastic Email MCP server](https://help.elasticemail.com/en/articles/12595879-elastic-email-mcp). Add it to any MCP client (VS Code, Cursor, Claude Code and others):

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

Prefer to run it yourself? Use the [self-hosted .NET MCP server](https://github.com/ElasticEmail/elasticemail-mcp-server).

## Skills

| Skill | What it covers |
| --- | --- |
| [`elastic-email-api`](skills/elastic-email-api/SKILL.md) | The full REST API v4 (15 endpoint groups), all 12 official SDKs, the MCP server, authentication, API limits, error handling and common workflows |

The skill keeps `SKILL.md` short and loads detailed references only when needed:

| Reference | Contents |
| --- | --- |
| [`rest-api-endpoints.md`](skills/elastic-email-api/references/rest-api-endpoints.md) | Every endpoint with parameters, request and response schemas, and required access levels |
| [`sdk-libraries.md`](skills/elastic-email-api/references/sdk-libraries.md) | Install, configuration and examples for each SDK |
| [`code-examples.md`](skills/elastic-email-api/references/code-examples.md) | Copy-paste examples for common operations in several languages |
| [`authentication.md`](skills/elastic-email-api/references/authentication.md) | API keys, access levels, SMTP credentials and MCP auth |
| [`mcp-tools.md`](skills/elastic-email-api/references/mcp-tools.md) | The Elastic Email MCP server's tools |

<details>
<summary><b>Repository layout</b></summary>

```text
elasticemail-skills/
└── skills/
    └── elastic-email-api/
        ├── SKILL.md                    # Skill entry point: overview, quick starts, workflows
        └── references/
            ├── rest-api-endpoints.md   # Complete API endpoint reference
            ├── sdk-libraries.md        # SDK setup and examples for all languages
            ├── code-examples.md        # Copy-paste code examples
            ├── authentication.md       # API keys, SMTP and MCP auth
            └── mcp-tools.md            # MCP server tool reference
```

</details>

## More examples

Runnable projects live in the [Elastic Email examples repository](https://github.com/ElasticEmail/elasticemail-examples), including [AI agent examples](https://github.com/ElasticEmail/elasticemail-examples/tree/main/ai-agents-elasticemail-examples) for LangChain, the OpenAI Agents SDK and the Vercel AI SDK.

## Supported languages

The skill knows the setup and conventions of every official Elastic Email SDK:

| Language | Package | Repository |
| --- | --- | --- |
| Python | `ElasticEmail` (PyPI) | [elasticemail-python](https://github.com/ElasticEmail/elasticemail-python) |
| C# / .NET | `ElasticEmail` (NuGet) | [elasticemail-csharp](https://github.com/ElasticEmail/elasticemail-csharp) |
| Java | `com.github.ElasticEmail:elasticemail-java` (JitPack) | [elasticemail-java](https://github.com/ElasticEmail/elasticemail-java) |
| PHP | `elasticemail/elasticemail-php` (Packagist) | [elasticemail-php](https://github.com/ElasticEmail/elasticemail-php) |
| JavaScript | `@elasticemail/elasticemail-client` (npm) | [elasticemail-js](https://github.com/ElasticEmail/elasticemail-js) |
| TypeScript (Angular) | `@elasticemail/elasticemail-client-ts-angular` (npm) | [elasticemail-ts-angular](https://github.com/ElasticEmail/elasticemail-ts-angular) |
| TypeScript (Axios) | `@elasticemail/elasticemail-client-ts-axios` (npm) | [elasticemail-ts-axios](https://github.com/ElasticEmail/elasticemail-ts-axios) |
| Go | `github.com/elasticemail/elasticemail-go/v4` | [elasticemail-go](https://github.com/ElasticEmail/elasticemail-go) |
| Ruby | `ElasticEmail` (RubyGems) | [elasticemail-ruby](https://github.com/ElasticEmail/elasticemail-ruby) |
| Rust | `ElasticEmail` (crates.io) | [elasticemail-rust](https://github.com/ElasticEmail/elasticemail-rust) |
| Perl | `ElasticEmail::*` modules (from GitHub) | [elasticemail-perl](https://github.com/ElasticEmail/elasticemail-perl) |
| Bash | `ElasticEmail` CLI script | [elasticemail-bash](https://github.com/ElasticEmail/elasticemail-bash) |

## Authentication

| Method | Where it's used | How |
| --- | --- | --- |
| API key | REST API and SDKs | `X-ElasticEmail-ApiKey` header |
| API key | MCP server | `X-Auth-Token` header |
| SMTP credentials | SMTP relay (`smtp.elasticemail.com`, port 2525) | Account email as username, API key or SMTP credential as password |

Give each API key only the access levels it needs (for example `SendHttp` for sending only). The skill's [authentication reference](skills/elastic-email-api/references/authentication.md) lists them all.

## API limits

- Up to 20 concurrent connections per account
- 600-second timeout per request
- 20 MB maximum email size, including attachments

## Versioning

Skills follow [semantic versioning](https://semver.org). The version is set in each skill's `SKILL.md` front matter (`metadata.version`) and published as a [GitHub release](https://github.com/ElasticEmail/elasticemail-skills/releases).

<details>
<summary><b>Current versions</b></summary>

| Skill | Version | API version |
| --- | --- | --- |
| `elastic-email-api` | 1.0.0 | v4 |

</details>

## Contributing

Contributions are welcome! Corrections to endpoints, SDK samples or workflows are especially useful. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request.

- 🐛 [Report a bug](https://github.com/ElasticEmail/elasticemail-skills/issues/new?template=bug_report.md)
- 💡 [Request a feature](https://github.com/ElasticEmail/elasticemail-skills/issues/new?template=feature_request.md)
- 🔒 [Report a security issue](SECURITY.md)

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md).

## Support

> [!IMPORTANT]
> The fastest way to get help is the **chat widget on [elasticemail.com](https://elasticemail.com)**. Our support team can help with your account, sending, deliverability and API questions.

- 💬 [Chat with support on elasticemail.com](https://elasticemail.com) (preferred)
- 📚 [API documentation](https://elasticemail.com/developers/api-documentation/rest-api)
- 🧪 [Examples repository](https://github.com/ElasticEmail/elasticemail-examples)
- 🐛 [GitHub issues](https://github.com/ElasticEmail/elasticemail-skills/issues), for problems with these skills only

## License

Released under the [MIT License](LICENSE). Copyright © 2025–2026 Elastic Email.
