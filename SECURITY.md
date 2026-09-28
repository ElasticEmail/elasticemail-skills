# Security Policy

## Supported versions

Security fixes are released for the latest version of each skill in this repository. Please update to the latest [release](https://github.com/ElasticEmail/elasticemail-skills/releases) before reporting an issue.

| Skill | Version | Supported          |
| ----- | ------- | ------------------ |
| `elastic-email-api` | 1.0.x | :white_check_mark: |

## Reporting a vulnerability

**Please do not open a public GitHub issue for security vulnerabilities.**

Report them privately by one of these methods:

- [GitHub private vulnerability reporting](https://github.com/ElasticEmail/elasticemail-skills/security/advisories/new)
- Email **integrations@elasticemail.com** with the subject `Security: elasticemail-skills`

Please include:

- A description of the issue and its impact (for example, instructions that could lead an assistant to leak an API key)
- Steps to reproduce, including the prompt and assistant used
- The affected skill and version

We will acknowledge your report, investigate, and keep you updated on the fix. Please give us reasonable time to release a fix before disclosing the issue publicly.

## API key safety

If you think an API key has been exposed (in a commit, log, issue, prompt transcript or screenshot), revoke it right away in your [Elastic Email API settings](https://app.elasticemail.com/marketing/settings/new/manage-api) and create a new one.
