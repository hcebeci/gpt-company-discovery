# Agent architecture

Great Designer creates five C-level executive agents, each limited to one direct subordinate manager; managers can create bounded worker pools.

| Executive | Manager | Workers |
|---|---|---|
| CTO | Engineering Manager | Harness, deployment, persistence, telemetry |
| CDO | Acquisition Manager | Search URL resolver, Similar pages capture, profile enrichment |
| COO | Frontier Manager | Durable queue, retry, leases, crash recovery |
| CTAXO | Industry Manager | Tech, Investment, Finance LinkedIn taxonomy experts |
| CQO | Verification Manager | Identity Truth Checker, Field Truth Checker, Graph Truth Checker |

## First engineering task
CTO and Engineering Manager review the open-source Codex harness and integrate the supported Codex SDK into a managed execution service. Reengineering the upstream harness is a project workstream; it is not completed simply by listing agents. Optional jev code must not be claimed as integrated unless the exact source repository has been located and analyzed.

## Queue rules
Persist all recommendation cards before visits. Missing URL -> PENDING_URL. Resolved URL -> PENDING_PAGE. Verified profile -> PENDING_QA. Approvals of three independent QA workers -> ACCEPTED. P1 Tech, Investment, Finance have equal priority. Within priority tiers order by discovery depth then discovery order; every sixth selection explores unknown and every twentieth explores other industries if present. Deduplicate by canonical LinkedIn company URL only.

## Evidence rules
A Similar pages edge is directed and must be visible in the parent page's recommendations. Search engine proximity or shared industry does not create an edge. A source receipt must preserve parent, child card data, URL when seen, page snapshot hash, extraction timestamp and expansion status. The expansion list is complete only if the authorized capture demonstrably reaches its end.

## Quality and stopping
Count 1,000 ONLY if canonical company URL and literal LinkedIn industry have been observed and the Identity, Fields and Graph QA workers independently approve the current source receipt. Never auto-promote the inherited 77 profiles. Preserve immutable historical observations, prompt versions, independent reviewer receipts, and an audit trail.

## Handoff
An executive or manager can create a same-role replacement. Store immutable JSON context (prompt version, receipt hashes, queue cursor, leases, unsolved exceptions, current counts, reviewer decisions) with a SHA-256 checksum. Replacement acknowledges the context before the old instance releases its tasks. Executives can propose new lower-agent prompts but activation requires benchmark pass and release approval.

## Deployment
Cloud worker (one replica) with durable disk; PostgreSQL and transactional leases required before horizontal scaling. Stop signal is 1,000 accepted, not 1,000 first-seen. Missing authorization or exhausted frontier is a blocked or waiting state, not successful completion.
