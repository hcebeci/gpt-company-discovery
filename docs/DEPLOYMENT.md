# Deployment — not live

The GitHub repository is connected. Railway is **not connected**. No cloud service has been launched.

## Required
- Railway connection, cloud account and persistent volume.
- OpenAI API key for Codex SDK agent calls.
- Web-search API key for LinkedIn company URL discovery.
- Authorized LinkedIn page and Similar pages evidence provider; no bypass of login, rate limits or CAPTCHAs.
- API usage and cloud budget.

## Implementation
Use the full prototype bundle in the user's ChatGPT downloads. It contains Python worker, SQLite queue, QA reviewers, role prompts, Dockerfile, and Railway settings. This GitHub branch contains review documentation only; it cannot be deployed as an application until the source files are transferred and tested on GitHub Actions.

In production use persistent storage and retryable leases. Accept a company only after observing LinkedIn URL, literal LinkedIn industry, and three separate identity/field/graph QA approvals. Stop only at 1,000 accepted records. Missing source access remains a blocked state, not success.

Do not put historical private data or API keys in this PUBLIC GitHub repository.
