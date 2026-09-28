# Elastic Email MCP Server — Complete Tool Reference

The Elastic Email MCP (Model Context Protocol) server enables AI tools to manage email operations directly. It connects to the Elastic Email API v4 and exposes 24 tools for email sending, contact management, campaign orchestration, template retrieval, segment management, and analytics. Tool names, parameters and behavior below are taken from the open-source server code (`src/Tools/*.cs`).

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

---

## Tool Reference

Every tool calls the API v4 with the API key from `X-Auth-Token`. Most tools return the raw API response body as a JSON string. Tools marked **bool** return only `true`/`false`, so on `false` there is no error detail. Check the key's access level and the input first.

### Emails

| Tool | Parameters | API call |
|---|---|---|
| `SendTransactionalEmail` | `data`: InputEmailData | `POST /emails/transactional` |
| `SendBulkEmails` | `emailData`: InputEmailData | `POST /emails` |

**InputEmailData**

| Field | Type | Notes |
|---|---|---|
| `To` | string[] | Recipient addresses |
| `Subject` | string | |
| `Content` | string | Plain text or HTML |
| `From` | string | Optional. If empty, the account's default sender is used; if that is also empty, the call fails |
| `TemplateName` | string | Optional. Name of an existing template; its content is applied to the message |
| `Attachments` | `{ FileName, Data }[]` | `Data` is the file bytes (base64 in JSON) |

Use `SendTransactionalEmail` for one-to-one, event-triggered mail and `SendBulkEmails` for marketing sends to many recipients.

### Campaigns

| Tool | Parameters | Returns | API call |
|---|---|---|---|
| `CreateCampaign` | `campaignData`: Campaign | JSON | `POST /campaigns` |
| `ListCampaigns` | — | JSON | `GET /campaigns` |
| `GetCampaign` | `name` | JSON | `GET /campaigns/{name}` |
| `UpdateCampaign` | `campaignData`: Campaign | JSON, or `"Not updated"` | `PUT /campaigns/{campaignData.Name}` |
| `PauseCampaign` | `name` | **bool** | `PUT /campaigns/{name}/pause` |

> **Warning:** `CreateCampaign` sends the campaign immediately when `Status` is `Active`. To review first, create it with `Status: "Draft"` (finish later) or `"Paused"` (delay sending).

**Campaign**

| Field | Required | Notes |
|---|---|---|
| `Name` | yes | Also the campaign's identifier for get/update/pause/statistics |
| `Recipients.ListNames` / `Recipients.SegmentNames` | at least one | Names of lists or segments to send to |
| `Status` | no | `Active`, `Paused`, `Draft` (others are read-only states: `Processing`, `Sending`, `Completed`, `Cancelled`, `Deleted`) |
| `Content[]` | no | One entry per template variant; more than one creates an A/X split test |
| `Content[].From` | yes, per entry | |
| `Content[].Subject` | yes, per entry | |
| `Content[].ReplyTo` | no | Defaults to `From` |
| `Content[].TemplateName` | no | Existing template to use as the body |
| `Content[].Poolname`, `AttachFiles`, `Utm` | no | IP pool, names of pre-uploaded files, UTM tags added to every link |
| `Options.ScheduleFor` | no | Send date/time |
| `Options.TrackOpens`, `Options.TrackClicks` | no | |
| `Options.DeliveryOptimization` | no | `None` (default), by engagement score, or by recipient's best open time |
| `Options.TriggerFrequency`, `Options.TriggerCount` | no | Repeat every N minutes, N times |
| `Options.SplitOptions` | no | Split-test settings; ignored with a single `Content` entry |

### Contacts

| Tool | Parameters | Returns | API call |
|---|---|---|---|
| `FetchContacts` | `limit` (default 20) | JSON | `GET /contacts?limit=` |
| `FetchContactHistory` | `email` | JSON | `GET /contacts/{email}/history` |
| `AddContact` | `email`, `firstName?`, `lastName?` | **bool** | `POST /contacts` |
| `DeleteContacts` | `contacts?`: string[], `rule?`: string | **bool** | `POST /contacts/delete` |
| `UploadContacts` | `fileName`, `base64Content`, `listName` | `"Success"` or error text | `POST /contacts/import` |

- `DeleteContacts` takes **either** a list of emails **or** a SQL-like `rule`, never both. Passing both, or neither, returns `false`.
- `UploadContacts` accepts `.csv`, `.txt`, `.json` or `.zip` content encoded as base64. If `listName` doesn't exist, it is created first. The import runs asynchronously; `"Success"` means accepted (HTTP 202), not finished.

### Lists

| Tool | Parameters | Returns | API call |
|---|---|---|---|
| `FetchLists` | — | JSON | `GET /lists` |
| `FetchList` | `listName` | JSON | `GET /lists/{name}` |
| `FetchListContacts` | `listName` | JSON | `GET /lists/{name}/contacts` |
| `CreateList` | `listName`, `emails`: string[] | JSON | `POST /lists` |
| `AddContactsToList` | `listName`, `emails`: string[] | JSON | `POST /lists/{name}/contacts` |
| `RemoveContactsFromList` | `listName`, `contacts?`: string[], `rule?`: string | **bool** | `POST /lists/{name}/contacts/remove` |

`RemoveContactsFromList` follows the same either-emails-or-rule restriction as `DeleteContacts`. It only removes contacts from the list; it does not delete them from the account.

### Segments

| Tool | Parameters | API call |
|---|---|---|
| `CreateSegment` | `segmentData`: `{ Name, Rule }` (both required) | `POST /segments` |
| `GetSegments` | — | `GET /segments` |
| `GetSegment` | `name` | `GET /segments/{name}` |

`Rule` uses the Elastic Email segment rule syntax (for example `Status = Engaged`). Syntax reference: https://help.elasticemail.com/en/articles/5162182-segment-rules

### Templates

| Tool | Parameters | API call |
|---|---|---|
| `FetchTemplates` | `scopes`: (`Personal` \| `Global`)[], `templateTypes?`: (`RawHTML` \| `DragDropEditor` \| `TemplateEditor`)[] | `GET /templates?scopeType=&templateTypes=` |
| `FetchTemplate` | `name` | `GET /templates/{name}` |

`scopes` is required: `Personal` is the account's own templates, `Global` is the Elastic Email gallery. `templateTypes` defaults to all three. The MCP server cannot create or delete templates; use the REST API (`POST /templates`, `DELETE /templates/{name}`) for that.

### Statistics

| Tool | Parameters | API call |
|---|---|---|
| `GetCampaignStatistics` | `name` | `GET /statistics/campaigns/{name}` |
| `GetAllCampaignStatistics` | — | `GET /statistics/campaigns` |

### System

| Tool | Parameters | Returns |
|---|---|---|
| `IsReady` | — | `"healthy"`, `"server up, api connection not healthy: <status>"` or `"not healthy"` |

Call `IsReady` first when a session starts failing. It tells a bad key or missing access level (server up, API not healthy) apart from an unreachable server.

---

## What the MCP Server Does Not Cover

The tools above are a subset of API v4. For domains, suppressions, email verification, files, event logs, inbound routing, sub-accounts, API keys and SMTP credentials, and template create/update/delete, use the REST API directly — see `rest-api-endpoints.md`.
