# Contributing to Elastic Email Agent Skills

Thanks for taking the time to contribute! This document explains how to report problems, suggest changes and submit pull requests.

## Before you start: how skills are structured

Each skill lives in its own folder under [`skills/`](skills) and follows the [Agent Skills](https://agentskills.io) format:

- `SKILL.md` is the entry point. Its YAML front matter (`name`, `description`, `license`, `metadata`) decides when an assistant loads the skill, and its body gives the overview, quick starts and workflows.
- `references/` holds the detailed material (endpoints, SDK usage, code examples, authentication, MCP tools). Assistants read these files only when a task needs them.

This means:

- **Keep `SKILL.md` focused.** Put long tables and exhaustive examples in `references/` and link to them from `SKILL.md`.
- **The `description` field is the trigger.** Changes to it affect when the skill activates, so explain the reason in your pull request.
- **Facts must match the API.** Endpoints, parameters and models come from the [Elastic Email REST API v4](https://elasticemail.com/developers/api-documentation/rest-api) and its [OpenAPI specification](https://api.elasticemail.com/public/v4/swagger). SDK samples must match the current release of the matching [SDK repository](https://github.com/ElasticEmail).

If you're not sure where a change belongs, open an issue first and ask.

## Reporting bugs

Search [existing issues](https://github.com/ElasticEmail/elasticemail-skills/issues) first. If nothing matches, open a new issue using the **Bug report** template and include:

- The skill and its version (from `metadata.version` in `SKILL.md`, e.g. `1.0.0`)
- The AI assistant and model you used (e.g. Claude Code with Claude Sonnet)
- The prompt you gave and what the assistant produced
- What was wrong: an outdated endpoint, a sample that doesn't run, the skill not activating, and so on

**Never paste your API key** into an issue, log, prompt transcript or code sample.

Questions about your Elastic Email account, sending limits, deliverability or billing aren't handled in this repository. The preferred way to get help is the **chat widget on [elasticemail.com](https://elasticemail.com)**.

Looking for sample code? See the [Elastic Email examples repository](https://github.com/ElasticEmail/elasticemail-examples), including the [AI agent examples](https://github.com/ElasticEmail/elasticemail-examples/tree/main/ai-agents-elasticemail-examples).

## Suggesting features

Open an issue using the **Feature request** template. Describe the use case first and the solution second. It helps us decide whether the change belongs in an existing skill, a new skill, an SDK or the API documentation.

## Security issues

Please do **not** report security vulnerabilities in public issues. See [SECURITY.md](SECURITY.md).

## Development setup

No build step is needed. To test your changes, install the skill from your working copy and try it with a real assistant:

```bash
git clone https://github.com/ElasticEmail/elasticemail-skills.git
cd elasticemail-skills
ln -s "$PWD/skills/elastic-email-api" ~/.claude/skills/elastic-email-api
```

Then start a new Claude Code session and run a few prompts that should (and shouldn't) trigger the skill. If you changed a code sample, run it against a test account.

## Pull requests

1. Fork the repository and create a branch from `master` (`git checkout -b fix/short-description`).
2. Keep each change focused. One logical change per pull request.
3. Check that relative links in `SKILL.md` and `references/` still resolve.
4. Bump `metadata.version` in `SKILL.md` if you change the skill's behavior or content.
5. Open a pull request against `master` and fill in the template.

A maintainer will review your pull request and may ask for changes.

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating, you agree to uphold it.

## License

By contributing, you agree that your contributions will be licensed under the [MIT License](LICENSE).
