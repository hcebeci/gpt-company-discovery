# Cloud deployment and operational runbook — Railway + GitHub

## Current actual status

**NOT DEPLOYED.** This ChatGPT session does not have connected GitHub/Railway accounts or authorized LinkedIn data source credentials. No cloud service ID, URL, job ID, or live agent exists. Installing/connecting the suggested Railway and GitHub integrations requires user action. This repository is a deployable implementation starter and has been unit-tested locally, not validated against live LinkedIn access or a remote production database.

## Deployment prerequisites

1. Connect GitHub and Railway within ChatGPT, or grant deployment access directly through the providers. Use a private GitHub repository for code and configuration (never commit API secrets or raw auth cookies).
2. Obtain `CODEX_API_KEY` separately for Codex SDK review/manager roles. Using ChatGPT interactively is not an API-key substitute.
3. Obtain a supported web-search credential (`BRAVE_SEARCH_API_KEY`) for LinkedIn URL discovery.
4. Obtain an **authorized evidence provider** exposing a URL accessible to the cloud worker with a JSON POST contract detailed below, including a permissible complete Similar pages list capture. LinkedIn explicitly prohibits unauthorized crawlers and browser automation. Do not bypass sign-in, CAPTCHA, rate limits or other access controls.
5. Reserve adequate processing/API budget. 1,000 accepted companies require at least 3,000 independent QA reviews if all are successful, plus discovery and manager roles; no costs are prepaid.

## Railway deployment steps

- Push project contents to a new private GitHub repository. Link repository to a new Railway service. Railway discovers `railway.json` and builds the `Dockerfile`.
- Add a persistent Railway volume attached to the service with mount path `/data`. Without it SQLite would be lost on redeploy. Under Railway attached volumes are owned by root; set the Railway variable `RAILWAY_RUN_UID=0` or configure volume permissions explicitly.
- Configure service variables from `.env.example`: `CODEX_API_KEY`, `BRAVE_SEARCH_API_KEY`, `AUTHORIZED_PROFILE_PROVIDER_URL`, `AUTHORIZED_PROVIDER_TOKEN`, `TARGET_COMPANIES=1000`, `DATA_DIR=/data`, `APP_DIR=/app`, `SUPERVISOR_ENABLED=1`, `POLL_SECONDS=60`.
- Keep Railway replica count **1**. Multiple replicas with the same SQLite volume are unsupported. Restart policy is `ALWAYS`.
- Inspect Railway logs for `WAITING_AUTHORIZED_SOURCE_PROVIDER`, `WAITING_NEW_CANDIDATES_OR_QA`, `QA_CYCLE_DONE`, `FETCH_ATTEMPTED`, and `COMPLETE`. The first three are not completion.
- Back up the volume with Railway's volume backups. To re-seed from previously extracted files use existing `seed/` snapshot; historical records are never counted approved until new evidence is ingested.

### Authorized provider JSON POST contract

Request (private network/API):

```json
{"linkedin_url":"https://www.linkedin.com/company/example/","capture_similar_pages_full":true}
```

Response (fields are literal observations from the source; example names below are **schema placeholders**, not collected companies):

```json
{
  "type":"profile",
  "source_url":"https://www.linkedin.com/company/example/",
  "snapshot":"The source-rendered company's heading, exact industry and About page text, at least 50 characters.",
  "profile":{"linkedin_url":"https://www.linkedin.com/company/example/","company_name":"Exact heading seen","industry_linkedin":"Exact displayed industry","company_headline":"...","company_description":"...","followers":null,"specialties":null,"company_size":null,"website":null,"headquarters":null,"founding_year":null},
  "cards_source_url":"https://www.linkedin.com/company/example/",
  "cards":[{"name":"Observed card heading","industry":"Observed preview industry","location":"Observed preview location","linkedin_url":null,"ordinal":1}],
  "expansion_confirmed":false,
  "expansion_proof":null
}
```

If no source permission is available, do not return a fabricated response. The worker will remain blocked in a retryable state. `expansion_confirmed` is ignored unless `expansion_proof` contains `snapshot_sha` and `verified_button_absent`, supported by the external provider's authenticated receipt.

## Manually import authorized observations

For a receipt file with `type=cards`, `type=profile`, or `type=qa`, upload it to `/data/inbox/filename.json` using Railway's volume file tool. The worker moves valid files into `/data/inbox/processed/` and invalid files into `/data/inbox/invalid/`. Source SHA and provenance are appended to SQLite. The sample `examples/cards-example.json` has zero cards and is not evidence.

CLI equivalents for developer diagnostics:

```sh
python3 src/cli.py bootstrap --data ./persistent_data
python3 src/cli.py status --data ./persistent_data
python3 src/cli.py export --data ./persistent_data
python3 -m unittest discover -s tests -v
```

## Stop, resume, and integrity

- Service runs continuously until **>=1000** distinct LinkedIn URLs have a sourced mandatory industry and approved Identity/Fields/Graph QA by distinct reviewers.
- Without work or authorization it enters `WAITING_...` and keeps its queue; it does not claim `COMPLETE`.
- On a restart it resumes pending leases, prior evidence, and prompt receipts. It will not delete an existing company snapshot. An unsafe change to original data should raise integrity audit rather than overwrite records.
- When agent roles are available, `src/role_supervisor.py` initializes Great Designer -> exactly five C-level -> one manager each, records decision receipts, and leaves worker allocation to each manager's prompt. `src/qa_orchestrator.py` invokes 3 separate Codex SDK QA reviewers as receipts become available.
- Codex SDK integration is implemented; **a fork/rewrite of the upstream Codex harness is not**. Reengineering work is a CTO-approved workstream and requires dedicated integration/regression tests.

## Cost and scaling

A single Railway volume means one writer and one worker. No rotating proxies or unauthorized LinkedIn automation. After 1,000-company acceptance, export `/data/exports/verified_profiles.csv`, `first_seen_candidates.csv`, `similar_pages_edges.csv`, and `stats.json`. For concurrency, replace SQLite with Postgres + transactional task leases, and rerun QA regression tests before deployment.

Official references: https://docs.railway.com/volumes ; https://github.com/openai/codex/blob/main/sdk/typescript/README.md ; https://www.linkedin.com/help/linkedin/answer/a1341387/prohibition-of-scraping-software