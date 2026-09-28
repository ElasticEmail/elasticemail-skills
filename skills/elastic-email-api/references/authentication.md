# Elastic Email API Authentication Guide

## API Key Authentication

All Elastic Email API v4 calls require an API key for authentication. The API key must be included in the HTTP request header.

### Header Format

```
x-elasticemail-apikey: YOUR_API_KEY
```

### Generating an API Key

1. Log in to your Elastic Email account at https://app.elasticemail.com
2. Navigate to **Settings** > **Manage API Keys**
3. Click **Create** (or use direct link: https://app.elasticemail.com/marketing/settings/new/create-api for Marketing plans or https://app.elasticemail.com/api/settings/new/create-api for API plans)
4. Configure the access level permissions for the key
5. Optionally set IP restrictions for security
6. Save and securely store the generated key

### API Key Limits

- Up to **15 API keys**
- Each sub-account can have its own API keys
- Each key can have different access level permissions
- Keys can have expiration date and restricted to specific IP addresses or IP ranges

---

## Access Levels

API keys can be configured with granular access levels. Each API endpoint requires a specific access level. Example access levels:

| Access Level | Description |
|---|---|
| `ViewContacts` | Read contact and list data |
| `ModifyContacts` | Create, update, delete contacts and lists |
| `ViewCampaigns` | Read campaign data |
| `ModifyCampaigns` | Create, update, delete campaigns |
| `SendHttp` | Send emails via API (transactional and bulk) |
| `ViewSettings` | Read domain and account settings |
| `ModifySettings` | Change domain and account settings |
| `ViewTemplates` | Read email templates |
| `ModifyTemplates` | Create, update, delete templates |
| `ViewReports` | Read statistics, events, and email logs |
| `Export` | Export contact data to files |
| `Security` | Manage API keys and SMTP credentials |
| `ViewSubAccounts` | Read sub-account information |
| `ModifySubAccounts` | Create and delete sub-accounts |
| `ViewFiles` | Read uploaded files |
| `ModifyFiles` | Upload and delete files |
| `VerifyEmails` | Submit emails for verification |
| `ViewEmailVerifications` | Read verification results |

### Security Best Practices

1. **Principle of least privilege** — Only grant the access levels each key actually needs
2. **IP restrictions** — Restrict API keys to known IP addresses when possible
3. **Separate keys** — Use different keys for different applications or environments
4. **Rotate keys** — Periodically rotate API keys, especially after team changes
5. **Never expose keys** — Do not commit API keys to version control or expose in client-side code
6. **Use environment variables** — Store API keys in environment variables, not in source code

---

## SMTP Authentication

In addition to the REST API, Elastic Email supports SMTP for sending emails.

### SMTP Settings

| Setting | Value |
|---|---|
| **Host** | `smtp.elasticemail.com` |
| **Port** | `2525` (recommended), `25`, `587`, or `465` (SSL) |
| **Username** | Your Elastic Email account email |
| **Password** | Your API key or SMTP-specific credential |
| **Encryption** | TLS/SSL recommended |

### Creating SMTP Credentials

SMTP credentials can be managed via the API:

```
POST /security/smtp   — Create new SMTP credential
GET  /security/smtp   — List all SMTP credentials
PUT  /security/smtp/{name}  — Update credential
DELETE /security/smtp/{name} — Delete credential
```

---

## SDK Authentication Examples

### Python
```python
import os
import ElasticEmail

configuration = ElasticEmail.Configuration()
configuration.api_key["apikey"] = os.environ["ELASTICEMAIL_API_KEY"]

with ElasticEmail.ApiClient(configuration) as api_client:
    # Use api_client for all API operations
    pass
```

### C#
```csharp
using System;
using ElasticEmail.Client;

var config = new Configuration();
config.ApiKey.Add("X-ElasticEmail-ApiKey", Environment.GetEnvironmentVariable("ELASTICEMAIL_API_KEY"));
```

### Java
```java
import com.elasticemail.client.ApiClient;
import com.elasticemail.client.Configuration;
import com.elasticemail.client.auth.ApiKeyAuth;

ApiClient apiClient = Configuration.getDefaultApiClient();
ApiKeyAuth apiKey = (ApiKeyAuth) apiClient.getAuthentication("apikey");
apiKey.setApiKey(System.getenv("ELASTICEMAIL_API_KEY"));
```

### PHP
```php
$config = ElasticEmail\Configuration::getDefaultConfiguration()
    ->setApiKey('X-ElasticEmail-ApiKey', getenv('ELASTICEMAIL_API_KEY'));
```

### Perl
```perl
my $emails = ElasticEmail::EmailsApi->new(
    api_key => { 'X-ElasticEmail-ApiKey' => $ENV{ELASTICEMAIL_API_KEY} },
);
```

### Bash (elasticemail-bash)
```bash
./ElasticEmail --host "https://api.elasticemail.com" contactsGet limit=10 \
  "X-ElasticEmail-ApiKey:$ELASTICEMAIL_API_KEY"
```

### JavaScript
```javascript
const ElasticEmail = require('@elasticemail/elasticemail-client');
const client = ElasticEmail.ApiClient.instance;
client.authentications['apikey'].apiKey = process.env.ELASTICEMAIL_API_KEY;
```

### TypeScript (Axios)
```typescript
import { Configuration, EmailsApi } from '@elasticemail/elasticemail-client-ts-axios';
const emailsApi = new EmailsApi(new Configuration({ apiKey: process.env.ELASTICEMAIL_API_KEY }));
```

### TypeScript (Angular)
```typescript
import { Configuration } from '@elasticemail/elasticemail-client-ts-angular';
// In the server-side (SSR) providers; never ship the key in a browser bundle
{ provide: Configuration, useFactory: () => new Configuration({ credentials: { apikey: () => process.env['ELASTICEMAIL_API_KEY'] } }) }
```

### Go
```go
client := ElasticEmail.NewAPIClient(ElasticEmail.NewConfiguration())
ctx := context.WithValue(context.Background(), ElasticEmail.ContextAPIKeys,
    map[string]ElasticEmail.APIKey{
        "apikey": {Key: os.Getenv("ELASTICEMAIL_API_KEY")},
    })
// pass ctx to every call, e.g. client.EmailsAPI.EmailsTransactionalPost(ctx)
```

### Ruby
```ruby
ElasticEmail.configure do |config|
  config.api_key['X-ElasticEmail-ApiKey'] = ENV.fetch('ELASTICEMAIL_API_KEY')
end
```

### cURL
```bash
curl -H "x-elasticemail-apikey: YOUR_API_KEY" \
     https://api.elasticemail.com/v4/contacts
```

---

## MCP Authentication

The Elastic Email MCP server authenticates via the `X-Auth-Token` header, which carries your API key. The key is passed in the MCP client configuration and the server uses it for all API calls.

### Hosted MCP Server (recommended)

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

### Self-hosted .NET MCP Server

When running the .NET MCP server locally (https://github.com/ElasticEmail/elasticemail-mcp-server):

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

The API key used for MCP requires the following permissions (view and modify): Account, Templates, Campaigns, Contacts, Files, Send HTTP. At least "view" access to Access Tokens is also required.

---

## Rate Limits and Constraints

| Constraint | Value |
|---|---|
| **Concurrent connections** | 20 maximum |
| **Request timeout** | 600 seconds |
| **Max email size** | 20 MB (message + attachments) |
| **Max contacts per POST** | 1,000 (use import for more) |
| **Max recipients per campaign** | Depends on pricing plan (no hard limit) |

### Rate Limit Handling

When you exceed the concurrent connection limit, the API returns `429 Too Many Requests`. Implement exponential backoff:

```python
import time
import ElasticEmail

def api_call_with_retry(func, max_retries=5):
    for attempt in range(max_retries):
        try:
            return func()
        except ElasticEmail.ApiException as e:
            if e.status == 429:
                wait = 2 ** attempt  # 1, 2, 4, 8, 16 seconds
                time.sleep(wait)
            else:
                raise
    raise Exception("Max retries exceeded")
```
