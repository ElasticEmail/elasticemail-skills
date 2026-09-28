# Elastic Email REST API v4 — Complete Endpoint Reference

Base URL: `https://api.elasticemail.com/v4`

Authentication: Send API key in header `X-ElasticEmail-ApiKey: YOUR_API_KEY` (header names are case-insensitive)

Request and response bodies are JSON with **PascalCase** field names (`Recipients`, `Content`, `ListName`, ...).

Errors return a 4xx/5xx status with a body like `{"Error": "APIKey Expired"}`. Note that a missing/invalid API key or missing access level returns **400**, not 401/403.

---

## Campaigns

### Load Campaigns
```
GET /campaigns
```
Returns a list of all campaigns (max 1000). Required Access Level: **ViewCampaigns**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `search` | string | No | Text fragment for searching campaign names |
| `offset` | integer | No | Number of items to skip (default: 0) |
| `limit` | integer | No | Maximum items returned (default: 0 = all) |

**Response:** `200 OK` — Array of Campaign objects

### Add Campaign
```
POST /campaigns
```
Creates a new campaign for processing. Required Access Level: **ModifyCampaigns**

**Request Body:** Campaign object (JSON)

| Field | Type | Required | Description |
|---|---|---|---|
| `Name` | string | Yes | Campaign name |
| `Recipients` | object | Yes | `{ "ListNames": [...], "SegmentNames": [...] }` |
| `Content` | array | No | Array of `{ From, ReplyTo, Subject, TemplateName, Poolname, AttachFiles, Utm }` (`From` required). Multiple items = A/X split campaign. Campaign content references a stored template via `TemplateName` (no inline body). |
| `Status` | string | No | `Deleted`, `Active`, `Processing`, `Sending`, `Completed`, `Paused`, `Cancelled`, `Draft` |
| `ExcludedRecipients` | object | No | Same shape as `Recipients` |
| `Options` | object | No | `DeliveryOptimization` (`None`, `ToEngagedFirst`, `ByOpenTime`), `TrackOpens`, `TrackClicks`, `ScheduleFor`, `TriggerFrequency`, `TriggerCount`, `SplitOptions`, `SendAtLocalTime` |

```json
{
  "Name": "spring-sale",
  "Status": "Draft",
  "Recipients": { "ListNames": ["Newsletter"] },
  "Content": [
    { "From": "sender@yourdomain.com", "Subject": "Spring sale", "TemplateName": "spring-sale-template" }
  ]
}
```

**Response:** `201 Created` — Campaign object

### Load Campaign
```
GET /campaigns/{name}
```
Returns specified campaign details. Required Access Level: **ViewCampaigns**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Name of the campaign |

**Response:** `200 OK` — Campaign object

### Update Campaign
```
PUT /campaigns/{name}
```
Updates an existing Active or Paused campaign. Required Access Level: **ModifyCampaigns**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Name of the campaign |

**Request Body:** Campaign object (JSON)

**Response:** `200 OK` — Campaign object

### Delete Campaign
```
DELETE /campaigns/{name}
```
Deletes the specified campaign (does not cancel emails in progress). Required Access Level: **ModifyCampaigns**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Name of the campaign |

**Response:** `200 OK`

### Pause Campaign
```
PUT /campaigns/{name}/pause
```
Pauses the specified campaign, cancelling queued emails. Required Access Level: **ModifyCampaigns**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Name of the campaign |

**Response:** `200 OK`

### Trigger Automation for Contact
```
POST /campaigns/automation/{name}/trigger
```
Manually trigger an Automation for a contact. Required Access Level: **ModifyAutomations**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Name of the automation |
| `contactEmail` | string | Yes (query) | Email of the contact to trigger the automation for |

**Response:** `200 OK`

---

## Contacts

### Load Contacts
```
GET /contacts
```
Returns a list of contacts. Required Access Level: **ViewContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `limit` | integer | No | Maximum items returned (default: 20) |
| `offset` | integer | No | Number of items to skip (default: 0) |

**Response:** `200 OK` — Array of Contact objects

### Add Contacts
```
POST /contacts
```
Add new contacts (up to 1000 per request; for more, use import). Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `listnames` | array | No (query) | List names to add contacts to (repeat the parameter: `?listnames=A&listnames=B`) |

**Request Body:** Array of ContactPayload objects (JSON) — `Email` (required), `Status`, `FirstName`, `LastName`, `CustomFields` (object; only existing custom fields), `Consent`

`Status` values: `Transactional`, `Engaged`, `Active`, `Bounced`, `Unsubscribed`, `Abuse`, `Inactive`, `Stale`, `NotConfirmed`

```json
[{ "Email": "john@example.com", "FirstName": "John", "LastName": "Doe", "Status": "Active" }]
```

**Response:** `200 OK` — Array of Contact objects

### Load Contact
```
GET /contacts/{email}
```
Returns detailed contact information. Required Access Level: **ViewContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | Yes (path) | Email address of the contact |

**Response:** `200 OK` — Contact object

### Update Contact
```
PUT /contacts/{email}
```
Updates a contact. Omitted fields remain unchanged. Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | Yes (path) | Email address of the contact |

**Request Body:** ContactUpdatePayload (JSON) — `FirstName`, `LastName`, `CustomFields`

**Response:** `200 OK` — Contact object

### Delete Contact
```
DELETE /contacts/{email}
```
Deletes the specified contact. Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | Yes (path) | Email address of the contact |

**Response:** `200 OK`

### Delete Contacts Bulk
```
POST /contacts/delete
```
Deletes contacts in bulk. Required Access Level: **ModifyContacts**

**Request Body:** EmailsPayload — `{ "Emails": ["a@example.com"] }` or `{ "Rule": "..." }` (provide one, not both)

**Response:** `200 OK`

### Export Contacts
```
POST /contacts/export
```
Request an export of specified contacts. Required Access Level: **Export**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `fileFormat` | string | No | Export format: Csv, Xml, Json |
| `rule` | string | No | Query for filtering (e.g., `Status = Engaged`) |
| `emails` | array | No | Specific emails to export |
| `compressionFormat` | string | No | None or Zip |
| `fileName` | string | No | Output filename |

**Response:** `202 Accepted` — ExportLink object

### Check Export Status
```
GET /contacts/export/{id}/status
```
Check current status of an export. Required Access Level: **Export**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string (GUID) | Yes (path) | ID of the export |

**Response:** `200 OK` — ExportStatus object

### Upload/Import Contacts
```
POST /contacts/import
```
Upload contacts from a CSV file. Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `listName` | string | No | Existing list to add contacts to |
| `encodingName` | string | No | File encoding |
| `fileUrl` | string | No | URL of CSV to import |

**Request Body:** `multipart/form-data` with `file` field (CSV, required column: Email). Options go in the query string, not the form.

**Response:** `202 Accepted` (import is processed asynchronously)

---

## Domains

### Load Domains
```
GET /domains
```
Returns all domains configured for the account. Required Access Level: **ViewSettings**

**Response:** `200 OK` — Array of DomainDetail objects

### Add Domain
```
POST /domains
```
Add a new domain. Required Access Level: **ModifySettings**

**Request Body:** DomainPayload (JSON) — `{ "Domain": "yourdomain.com", "SetAsDefault": false }`

**Response:** `201 Created` — DomainDetail object

### Load Domain
```
GET /domains/{domain}
```
Retrieve a specific domain. Required Access Level: **ViewSettings**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `domain` | string | Yes (path) | Domain name |

**Response:** `200 OK` — DomainData object

### Update Domain
```
PUT /domains/{domain}
```
Updates the specified domain. Required Access Level: **ModifySettings**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `domain` | string | Yes (path) | Domain name |

**Request Body:** DomainUpdatePayload (JSON) — `CertificateStatus`, `VERP`, `CustomBouncesDomain`, `IsCustomBouncesDomainDefault`

**Response:** `200 OK` — DomainDetail object

### Delete Domain
```
DELETE /domains/{domain}
```
Deletes the specified domain. Required Access Level: **ModifySettings**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `domain` | string | Yes (path) | Domain name |

**Response:** `200 OK`

### Check Domain Restriction
```
GET /domains/{domain}/restricted
```
Check if a domain is from a free provider or restricted. Required Access Level: **ViewSettings**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `domain` | string | Yes (path) | Domain name |

**Response:** `200 OK` — boolean

### Verify Domain
```
PUT /domains/{domain}/verification
```
Verifies DNS records for the specified domain. Required Access Level: **ModifySettings**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `domain` | string | Yes (path) | Domain name |

**Request Body:** JSON string with the tracking type: `"None"`, `"Delete"`, `"Http"`, `"ExternalHttps"`, `"InternalCertHttps"`, `"LetsEncryptCert"`

**Response:** `200 OK` — DomainData object

### Set Default Domain
```
PATCH /domains/{email}/default
```
Sets a verified email as default sender. Required Access Level: **ModifySettings**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | Yes (path) | Default sender email (e.g., mail@yourdomain.com) |

**Response:** `200 OK` — DomainDetail object

---

## Emails

### Send Bulk Emails
```
POST /emails
```
Send bulk/merge email to multiple recipients. Required Access Level: **SendHttp**

**Request Body:** EmailMessageData (JSON) — `Recipients`, `Content`, `Options`

| Field | Type | Required | Description |
|---|---|---|---|
| `Recipients` | array | Yes | Flat array of `{ "Email": "...", "Fields": { "firstname": "Ann" } }` — one personalized copy per recipient (up to 1000 per call) |
| `Content` | object | Yes | EmailContent: `From` (required), `Subject`, `Body` (array of `{ ContentType, Content, Charset }`), `TemplateName`, `Merge`, `Attachments` (`{ BinaryContent, Name, ContentType }`), `AttachFiles`, `Headers`, `ReplyTo`, `EnvelopeFrom`, `Postback`, `Utm` |
| `Options` | object | No | `TimeOffset` (minutes, max 35 days), `PoolName`, `ChannelName`, `Encoding`, `TrackOpens`, `TrackClicks` |

`Body[].ContentType` values: `HTML`, `PlainText`, `AMP`, `CSS`. Merge placeholders use single braces: `{firstname}`.

**Response:** `200 OK` — EmailSend object (contains TransactionID and MessageID)

### View Email
```
GET /emails/{msgid}/view
```
Returns email details for viewing. Required Access Level: **None**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `msgid` | string | Yes (path) | Message identifier |

**Response:** `200 OK` — EmailData object

### Get Email Status
```
GET /emails/{transactionid}/status
```
Get status details of an email transaction. Required Access Level: **ViewReports**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `transactionid` | string | Yes (path) | Transaction identifier |
| `showFailed` | boolean | No | Include bounced addresses |
| `showSent` | boolean | No | Include sent addresses |
| `showDelivered` | boolean | No | Include delivered addresses |
| `showPending` | boolean | No | Include pending addresses |
| `showOpened` | boolean | No | Include opened addresses |
| `showClicked` | boolean | No | Include clicked addresses |
| `showAbuse` | boolean | No | Include abuse-reported addresses |
| `showUnsubscribed` | boolean | No | Include unsubscribed addresses |
| `showErrors` | boolean | No | Include error messages |
| `showMessageIDs` | boolean | No | Include all MessageIDs |

**Response:** `200 OK` — EmailJobStatus object

### Send Bulk Emails CSV
```
POST /emails/mergefile
```
Send to contacts from a CSV file with merge fields. Required Access Level: **SendHttp**

**Request Body:** MergeEmailPayload (JSON) — `MergeFile` (required, `{ "BinaryContent": "<base64 CSV>", "Name": "recipients.csv" }`), `Content` (required, EmailContent), `Options`

CSV format: First column must be email, include header row. Merge fields accessible via `{merge}` tags.

**Response:** `200 OK` — EmailSend object

### Send Transactional Email
```
POST /emails/transactional
```
Send a transactional email (recipients will be known to each other). Required Access Level: **SendHttp**

**Request Body:** EmailTransactionalMessageData (JSON) — `Recipients` (required, `{ "To": [...], "CC": [...], "BCC": [...] }`, `To` required, up to 50 recipients), `Content` (required, EmailContent — same as bulk), `Options`

**Response:** `200 OK` — EmailSend object

---

## Events

### Load Events
```
GET /events
```
Returns delivery event history. Required Access Level: **ViewReports**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `eventTypes` | array | No | Event types to load: `Submission`, `FailedAttempt`, `Error`, `Sent`, `Open`, `Click`, `Unsubscribe`, `Complaint`, `Bounce`, `TransactionalUnsubscribe`, `Suppress` |
| `from` | datetime | No | Start date (`YYYY-MM-DDThh:mm:ss`) |
| `to` | datetime | No | End date (`YYYY-MM-DDThh:mm:ss`) |
| `orderBy` | string | No | `DateDescending` or `DateAscending` |
| `limit` | integer | No | Maximum items (max 1000) |
| `offset` | integer | No | Items to skip |

**Response:** `200 OK` — Array of RecipientEvent objects

### Load Events by Transaction
```
GET /events/{transactionid}
```
Returns events for a specific transaction. Required Access Level: **ViewReports**

Parameters: `from`, `to`, `orderBy`, `limit`, `offset` (same as Load Events, no `eventTypes`) plus:

| Parameter | Type | Required | Description |
|---|---|---|---|
| `transactionid` | string | Yes (path) | Transaction identifier |

**Response:** `200 OK` — Array of RecipientEvent objects

### Load Channel Events
```
GET /events/channels/{name}
```
Returns delivery events for a channel. Required Access Level: **ViewReports**

Parameters same as Load Events plus:

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Channel name |

**Response:** `200 OK` — Array of RecipientEvent objects

### Export Events
```
POST /events/export
```
Export the delivery event log to a file. Required Access Level: **Export**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `eventTypes` | array | No | Event types to export (see Load Events) |
| `from` | datetime | No | Start date |
| `to` | datetime | No | End date |
| `fileFormat` | string | No | `Csv`, `Xml`, `Json` |
| `compressionFormat` | string | No | `None` or `Zip` |
| `fileName` | string | No | Output filename including extension |

**Response:** `202 Accepted` — ExportLink object (`Link`, `PublicExportID`)

### Check Events Export Status
```
GET /events/export/{id}/status
```
Check the status of an events export. Required Access Level: **Export**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string (GUID) | Yes (path) | ID of the export |

**Response:** `200 OK` — ExportStatus (`Error`, `Loading`, `Ready`, `Expired`)

### Export Channel Events
```
POST /events/channels/{name}/export
```
Export a channel's delivery events to a file. Required Access Level: **Export**

Parameters same as Export Events plus `name` (path, channel name).

**Response:** `202 Accepted` — ExportLink object

### Check Channel Export Status
```
GET /events/channels/export/{id}/status
```
Check the status of a channel events export. Required Access Level: **Export**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string (GUID) | Yes (path) | ID of the export |

**Response:** `200 OK` — ExportStatus

---

## Files

### List Files
```
GET /files
```
Returns a list of all uploaded files. Required Access Level: **ViewFiles**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `limit` | integer | No | Maximum items |
| `offset` | integer | No | Items to skip |

**Response:** `200 OK` — Array of FileInfo objects

### Upload File
```
POST /files
```
Upload a file to your account. Required Access Level: **ModifyFiles**

**Request Body:** FilePayload (JSON) — `{ "BinaryContent": "<base64>", "Name": "invoice.pdf", "ContentType": "application/pdf" }` (`BinaryContent` required). Uploaded files can be attached to emails by name via `Content.AttachFiles`.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `expiresAfterDays` | integer | No (query) | Auto-delete after N days |

**Response:** `201 Created` — FileInfo object

### Load File Details
```
GET /files/{name}/info
```
Returns file details. Required Access Level: **ViewFiles**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | File name including extension |

**Response:** `200 OK` — FileInfo object

### Delete File
```
DELETE /files/{name}
```
Deletes the specified file. Required Access Level: **ModifyFiles**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | File name |

**Response:** `200 OK`

### Download File
```
GET /files/{name}
```
Downloads the file content. Required Access Level: **ViewFiles**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | File name |

**Response:** `200 OK` — File content (binary)

---

## Inbound Route

### Get Inbound Routes
```
GET /inboundroute
```
Returns all inbound routes. Required Access Level: **ViewSettings**

**Response:** `200 OK` — Array of InboundRoute objects

### Create Inbound Route
```
POST /inboundroute
```
Create a new inbound route. Required Access Level: **ModifySettings**

**Request Body:** InboundPayload (JSON)

| Field | Type | Required | Description |
|---|---|---|---|
| `Name` | string | Yes | Route name |
| `Filter` | string | Yes | Value to match (email address or subject) |
| `FilterType` | string | Yes | `EmailAddress` or `Subject` |
| `ActionType` | string | Yes | `ForwardToEmail`, `NotifyViaHttp`, or `Stop` |
| `EmailAddress` | string | No | Forward target (for `ForwardToEmail`) |
| `HttpAddress` | string | No | URL to notify (for `NotifyViaHttp`) |

**Response:** `200 OK` — InboundRoute object

### Get Inbound Route
```
GET /inboundroute/{id}
```
Load a specific inbound route. Required Access Level: **ViewSettings**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string (GUID) | Yes (path) | Route ID |

**Response:** `200 OK` — InboundRoute object

### Update Inbound Route
```
PUT /inboundroute/{id}
```
Update an inbound route. Required Access Level: **ModifySettings**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string (GUID) | Yes (path) | Route ID |

**Request Body:** InboundPayload (JSON)

**Response:** `200 OK` — InboundRoute object

### Delete Inbound Route
```
DELETE /inboundroute/{id}
```
Deletes an inbound route. Required Access Level: **ModifySettings**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string (GUID) | Yes (path) | Route ID |

**Response:** `200 OK`

### Update Inbound Route Sorting
```
PUT /inboundroute/order
```
Change the order in which inbound routes are evaluated. Required Access Level: **ViewSettings** (as stated in the OpenAPI spec)

**Request Body:** Array of `{ "PublicInboundId": "<route id>", "SortOrder": 1 }` (`1` = evaluated first)

**Response:** `200 OK` — Array of InboundRoute objects

---

## Lists

### Load Lists
```
GET /lists
```
Returns all contact lists. Required Access Level: **ViewContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `limit` | integer | No | Maximum items (default: 200) |
| `offset` | integer | No | Items to skip (default: 0) |

**Response:** `200 OK` — Array of ContactsList objects

### Add List
```
POST /lists
```
Creates a new contact list. Required Access Level: **ModifyContacts**

**Request Body:** ListPayload (JSON) — ListName required, optionally AllowUnsubscribe and Emails

**Response:** `201 Created` — ContactsList object

### Load List
```
GET /lists/{name}
```
Returns a specific list. Required Access Level: **ViewContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | List name |

**Response:** `200 OK` — ContactsList object

### Update List
```
PUT /lists/{name}
```
Update an existing list. Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | List name |

**Request Body:** ListUpdatePayload (JSON) — `NewListName`, `AllowUnsubscribe`

**Response:** `200 OK` — ContactsList object

### Delete List
```
DELETE /lists/{name}
```
Deletes the list and removes all contacts from it (the contacts themselves are not deleted). Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | List name |

**Response:** `200 OK`

### Load Contacts In List
```
GET /lists/{listname}/contacts
```
Returns contacts in a list. Required Access Level: **ViewContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `listname` | string | Yes (path) | List name |
| `limit` | integer | No | Maximum items |
| `offset` | integer | No | Items to skip |

**Response:** `200 OK` — Array of Contact objects

### Add Contacts to List
```
POST /lists/{name}/contacts
```
Add existing contacts to a list. Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | List name |

**Request Body:** EmailsPayload (JSON) — `{ "Emails": ["john@example.com"] }` or `{ "Rule": "..." }` (contacts must already exist)

**Response:** `200 OK` — ContactsList object

### Remove Contacts from List
```
POST /lists/{name}/contacts/remove
```
Remove contacts from a list (contacts are not deleted). Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | List name |

**Request Body:** EmailsPayload (JSON) — `{ "Emails": [...] }` or `{ "Rule": "..." }`

**Response:** `200 OK`

---

## Security

### Get API Keys
```
GET /security/apikeys
```
Returns all API keys. Required Access Level: **ViewAccessTokens**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `subaccount` | string | No (query) | Email of a sub-account whose API keys to list |

**Response:** `200 OK` — Array of ApiKey objects

### Add API Key
```
POST /security/apikeys
```
Creates a new API key. Required Access Level: **ModifyAccessTokens**

**Request Body:** ApiKeyPayload (JSON) — `Name` (required), `AccessLevel` (required, array such as `["SendHttp", "ViewReports"]`), `Expires`, `RestrictAccessToIPRange`, `Subaccount` (email; creates the key for a sub-account)

**Response:** `201 Created` — NewApiKey object

### Get API Key
```
GET /security/apikeys/{name}
```
Returns a specific API key. Required Access Level: **ViewAccessTokens**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Key name |
| `subaccount` | string | No (query) | Email of the sub-account that owns it |

**Response:** `200 OK` — ApiKey object

### Update API Key
```
PUT /security/apikeys/{name}
```
Update an API key. Required Access Level: **ModifyAccessTokens**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Key name |

**Request Body:** ApiKeyPayload (JSON)

**Response:** `200 OK` — ApiKey object

### Delete API Key
```
DELETE /security/apikeys/{name}
```
Deletes an API key. Required Access Level: **ModifyAccessTokens**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Key name |
| `subaccount` | string | No (query) | Email of the sub-account that owns it |

**Response:** `200 OK`

### Get SMTP Credentials
```
GET /security/smtp
```
Returns all SMTP credentials. Required Access Level: **ViewAccessTokens**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `subaccount` | string | No (query) | Email of a sub-account whose credentials to list |

**Response:** `200 OK` — Array of SmtpCredentials objects

### Add SMTP Credential
```
POST /security/smtp
```
Creates new SMTP credentials. Required Access Level: **ModifyAccessTokens**

**Request Body:** SmtpCredentialsPayload (JSON) — `Name` (required, must be a valid email address; used as the SMTP username), `Expires`, `RestrictAccessToIPRange`, `Subaccount`

**Response:** `201 Created` — NewSmtpCredentials object

### Get SMTP Credential
```
GET /security/smtp/{name}
```
Returns a specific SMTP credential. Required Access Level: **ViewAccessTokens**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Credential name |
| `subaccount` | string | No (query) | Email of the sub-account that owns it |

**Response:** `200 OK` — SmtpCredentials object

### Update SMTP Credential
```
PUT /security/smtp/{name}
```
Update SMTP credentials. Required Access Level: **ModifyAccessTokens**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Credential name |

**Request Body:** SmtpCredentialsPayload (JSON)

**Response:** `200 OK` — SmtpCredentials object

### Delete SMTP Credential
```
DELETE /security/smtp/{name}
```
Deletes SMTP credentials. Required Access Level: **ModifyAccessTokens**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Credential name |
| `subaccount` | string | No (query) | Email of the sub-account that owns it |

**Response:** `200 OK`

---

## Segments

### Load Segments
```
GET /segments
```
Returns all segments. Required Access Level: **ViewContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `limit` | integer | No | Maximum items |
| `offset` | integer | No | Items to skip |

**Response:** `200 OK` — Array of Segment objects

### Add Segment
```
POST /segments
```
Creates a new segment. Required Access Level: **ModifyContacts**

**Request Body:** SegmentPayload (JSON)

Segment rule help: https://help.elasticemail.com/en/articles/5162182-segment-rules

**Response:** `201 Created` — Segment object

### Load Segment
```
GET /segments/{name}
```
Returns a specific segment. Required Access Level: **ViewContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Segment name |

**Response:** `200 OK` — Segment object

### Update Segment
```
PUT /segments/{name}
```
Updates a segment. Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Segment name |

**Request Body:** SegmentPayload (JSON)

**Response:** `200 OK` — Segment object

### Delete Segment
```
DELETE /segments/{name}
```
Deletes a segment. Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Segment name |

**Response:** `200 OK`

---

## Statistics

### Load Statistics
```
GET /statistics
```
Returns sending statistics. Required Access Level: **ViewReports**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `from` | datetime | Yes | Start date |
| `to` | datetime | No | End date (default: now) |

**Response:** `200 OK` — LogStatusSummary object

### Load Campaigns Stats
```
GET /statistics/campaigns
```
Returns campaign statistics. Required Access Level: **ViewReports**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `limit` | integer | No | Maximum items (default: 0) |
| `offset` | integer | No | Items to skip (default: 0) |

**Response:** `200 OK` — Array of ChannelLogStatusSummary objects

### Load Campaign Stats
```
GET /statistics/campaigns/{name}
```
Returns stats for a specific campaign. Required Access Level: **ViewReports**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Campaign name |

**Response:** `200 OK` — ChannelLogStatusSummary object

### Load Channels Stats
```
GET /statistics/channels
```
Returns channel statistics. Required Access Level: **ViewReports**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `limit` | integer | No | Maximum items |
| `offset` | integer | No | Items to skip |

**Response:** `200 OK` — Array of ChannelLogStatusSummary objects

### Load Channel Stats
```
GET /statistics/channels/{name}
```
Returns stats for a specific channel. Required Access Level: **ViewReports**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Channel name |

**Response:** `200 OK` — ChannelLogStatusSummary object

---

## SubAccounts

### Load SubAccounts
```
GET /subaccounts
```
Returns all sub-accounts. Required Access Level: **ViewSubAccounts**

**Response:** `200 OK` — Array of SubAccountInfo objects

### Add SubAccount
```
POST /subaccounts
```
Creates a new sub-account. Required Access Level: **ModifySubAccounts**

**Request Body:** SubaccountPayload (JSON)

**Response:** `201 Created` — SubAccountInfo object

### Get SubAccount
```
GET /subaccounts/{email}
```
Returns a specific sub-account. Required Access Level: **ViewSubAccounts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | Yes (path) | Sub-account email |

**Response:** `200 OK` — SubAccountInfo object

### Delete SubAccount
```
DELETE /subaccounts/{email}
```
Deletes a sub-account. Required Access Level: **ModifySubAccounts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | Yes (path) | Sub-account email |

**Response:** `200 OK`

---

## Suppressions

### Get Suppressions
```
GET /suppressions
```
Returns all suppressions. Required Access Level: **ViewContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `search` | string | No | Search text |
| `limit` | integer | No | Maximum items (default: 20) |
| `offset` | integer | No | Items to skip (default: 0) |

**Response:** `200 OK` — Array of Suppression objects

### Get Bounces
```
GET /suppressions/bounces
```
Returns bounce suppressions. Required Access Level: **ViewContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `search` | string | No | Search text |
| `limit` | integer | No | Maximum items |
| `offset` | integer | No | Items to skip |

**Response:** `200 OK` — Array of Suppression objects

### Add Bounces
```
POST /suppressions/bounces
```
Add email addresses to bounce list. Required Access Level: **ModifyContacts**

**Request Body:** Array of email strings

**Response:** `200 OK` — Array of Suppression objects

### Delete Bounce
```
DELETE /suppressions/bounces
```
Remove an email from bounce list. Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | Yes (query) | Email to remove |

**Response:** `200 OK`

### Get Complaints
```
GET /suppressions/complaints
```
Returns complaint suppressions. Required Access Level: **ViewContacts**

Parameters: `search`, `limit`, `offset` (same as bounces)

**Response:** `200 OK` — Array of Suppression objects

### Add Complaints
```
POST /suppressions/complaints
```
Add emails to complaints list. Required Access Level: **ModifyContacts**

**Request Body:** Array of email strings

**Response:** `200 OK` — Array of Suppression objects

### Delete Complaint
```
DELETE /suppressions/complaints
```
Remove email from complaints list. Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | Yes (query) | Email to remove |

**Response:** `200 OK`

### Get Unsubscribes
```
GET /suppressions/unsubscribes
```
Returns unsubscribe suppressions. Required Access Level: **ViewContacts**

Parameters: `search`, `limit`, `offset` (same as bounces)

**Response:** `200 OK` — Array of Suppression objects

### Add Unsubscribes
```
POST /suppressions/unsubscribes
```
Add emails to unsubscribe list. Required Access Level: **ModifyContacts**

**Request Body:** Array of email strings

**Response:** `200 OK` — Array of Suppression objects

### Delete Unsubscribe
```
DELETE /suppressions/unsubscribes
```
Remove email from unsubscribe list. Required Access Level: **ModifyContacts**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | Yes (query) | Email to remove |

**Response:** `200 OK`

---

## Templates

### Load Templates
```
GET /templates
```
Returns all templates. Required Access Level: **ViewTemplates**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `scopeType` | array | Yes | Template scopes: Personal, Global |
| `templateTypes` | array | No | Template types filter |
| `limit` | integer | No | Maximum items (default: 100) |
| `offset` | integer | No | Items to skip (default: 0) |

**Response:** `200 OK` — Array of Template objects

### Add Template
```
POST /templates
```
Creates a new template. Required Access Level: **ModifyTemplates**

**Request Body:** TemplatePayload (JSON) — includes Name, Subject, Body, TemplateScope

**Response:** `201 Created` — Template object

### Load Template
```
GET /templates/{name}
```
Returns a specific template. Required Access Level: **ViewTemplates**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Template name |

**Response:** `200 OK` — Template object

### Update Template
```
PUT /templates/{name}
```
Updates an existing template. Required Access Level: **ModifyTemplates**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Template name |

**Request Body:** TemplatePayload (JSON)

**Response:** `200 OK` — Template object

### Delete Template
```
DELETE /templates/{name}
```
Deletes a template. Required Access Level: **ModifyTemplates**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes (path) | Template name |

**Response:** `200 OK`

---

## Verifications

### Verify Email
```
POST /verifications
```
Verify a single email address. Required Access Level: **VerifyEmails**

**Request Body:** Email address string

**Response:** `200 OK` — EmailValidationResult object

### Get Email Verification Result
```
GET /verifications/{email}
```
Returns verification result for an email. Required Access Level: **ViewEmailVerifications**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | Yes (path) | Email address |

**Response:** `200 OK` — EmailValidationResult object

### Verify File of Emails
```
POST /verifications/files
```
Upload a file of emails for bulk verification. Required Access Level: **VerifyEmails**

**Request Body:** `multipart/form-data` with `file` field (CSV or TXT)

**Response:** `200 OK` — VerificationFileResult object

### Get File Verification Result
```
GET /verifications/files/{id}
```
Returns results of a file verification. Required Access Level: **ViewEmailVerifications**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string | Yes (path) | Verification file ID |

**Response:** `200 OK` — VerificationFileResult object

### Download Verification Results
```
GET /verifications/files/{id}/result
```
Download the results of a verification file. Required Access Level: **ViewEmailVerifications**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string | Yes (path) | Verification file ID |

**Response:** `200 OK` — VerificationFileResult with download link

### Get Verification Files
```
GET /verifications/files/result
```
Returns all verification file results. Required Access Level: **ViewEmailVerifications**

**Response:** `200 OK` — Array of VerificationFileResult objects
