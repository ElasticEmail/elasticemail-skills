## Elastic Email Agent Skills 1.0.0

The first release of the official Elastic Email agent skills. Install one skill and Claude, or any assistant that supports Agent Skills, knows how to use the Elastic Email REST API v4, the official SDKs and the MCP server.

```bash
npx skills add ElasticEmail/elasticemail-skills --skill elastic-email-api --global
```

### What's in this release

- **`elastic-email-api` skill (1.0.0)**: activates when a request mentions Elastic Email, sending email, contacts, campaigns, templates, domains, suppressions or verifications.
  - **REST API v4**: all 15 endpoint groups, from Emails, Campaigns and Contacts to Security and SubAccounts, with parameters, schemas and required access levels (`references/rest-api-endpoints.md`).
  - **12 official SDKs**: setup and examples for Python, C#, Java, PHP, JavaScript, TypeScript (Angular and Axios), Go, Ruby, Rust, Perl and Bash (`references/sdk-libraries.md`, `references/code-examples.md`).
  - **MCP server**: hosted (`https://mcp.elasticemail.com`) and self-hosted .NET setup, plus the tool reference (`references/mcp-tools.md`).
  - **Authentication**: API keys, access levels, SMTP and MCP auth (`references/authentication.md`).
  - **Workflows and guardrails**: transactional sends, campaigns, contact imports, email verification and domain setup, plus API limits, error codes and troubleshooting.

### Repository

- The repository is renamed from `ElasticEmail/skills` to `ElasticEmail/elasticemail-skills` to match the SDK repositories. GitHub redirects the old URL.
- New README in the same style as the Elastic Email SDKs, with install options for the `skills` CLI, Context7, Claude Code and Claude.ai.
- Added `CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md` and issue and pull request templates.

### Compatibility

- Claude Code, Claude.ai, Claude Desktop and other clients that support `SKILL.md` Agent Skills
- Elastic Email REST API v4

**Full changelog:** https://github.com/ElasticEmail/elasticemail-skills/commits/v1.0.0
