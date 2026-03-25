# Elastic Email MCP Server — Complete Tool Reference

The Elastic Email MCP (Model Context Protocol) server enables AI tools to manage email operations directly. It connects to the Elastic Email API v4 and exposes a set of tools for email sending, contact management, campaign orchestration, template retrieval, segment management, and analytics.

**Hosted URL:** `https://mcp.elasticemail.com`
**Documentation:** https://help.elasticemail.com/en/articles/12595879-elastic-email-mcp
**Self-hosted .NET server:** https://github.com/ElasticEmail/elasticemail-mcp-server

---

## Setup

### Option 1: Hosted MCP Server (recommended)

The official Elastic Email MCP server is available as a hosted service — no local installation required. Add the following to your MCP client configuration (VS Code, Cursor, or any MCP-compatible client):

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

### Option 2: Self-hosted .NET Server

Elastic Email also provides an open-source .NET MCP server that you can run locally. Source code: https://github.com/ElasticEmail/elasticemail-mcp-server

**Requirements:** .NET SDK 10 or higher, open port 5001 for HTTP.

After building and running the server locally, add the following to your MCP client configuration:

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

### Prerequisites
- A valid Elastic Email API key with appropriate access levels (required permissions: Account, Templates, Campaigns, Contacts, Files, Send HTTP — view and modify; at least "view" access to Access Tokens)
- MCP-compatible AI client (VS Code with GitHub Copilot in Agent mode, Cursor, Claude Desktop, ChatGPT, etc.)
- For the self-hosted option: .NET SDK 10+
