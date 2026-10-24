# e2vc · Cloud agent discovery system, version 5

**Goal:** 1,000 companies, **mandatory** canonical LinkedIn company URL and literal LinkedIn industry; optional size, website, headquarters, year, description, follower count, specialties, headline. All fields evidence-backed. **Current deployment status: NOT RUNNING IN THE CLOUD.**

## Historical checkpoint, not 1,000 verified companies

- **77** inherited page-linked rows; independently reverified in this version: **0**.
- **120** historical Similar pages observations, with full-list expansion unconfirmed.
- **103** first-seen candidate records loaded in this package's durable seed (some unresolved URL duplicates remain separate until adjudicated).
- **0** profiles accepted by independent three-reviewer QA at the newly stricter gate.

## What has been implemented

- Python cloud loop and Docker/Railway configuration with restart and persistent SQLite volume.
- Brave Search API URL resolver (optional key), deduplication, three-tier priority ordering.
- First-seen append-only card observations, directed graph edges and unresolved URL candidates.
- Authorized provider JSON input for actual company page evidence + optional Similar pages capture.
- Source-hash-based fact checks and Codex SDK three-role QA gate.
- Great Designer, CTO, CDO, COO, CTAXO, CQO with one manager each; three industry experts; three QA agents; durable handoff and prompt versioning interfaces.
- Historical dataset preserved unchanged under `seed/`, plus exports and tests.

## What has NOT happened

- Railway/GitHub **not connected or deployed** in this session.
- No authorized programmatic LinkedIn Similar pages extraction provider connected.
- No 1,000-company background run or 3,000 agent reviews performed.
- Source code from a named `jev` project was not identified, cloned, or adapted; no upstream Codex fork has been modified. The documented Codex SDK is integrated as the orchestration API instead.

## Layout

| Location | Purpose |
|---|---|
| `agents/AGENT_PROMPTS_FINAL.md` | All final role prompts |
| `agents/ARCHITECTURE.md` | Role hierarchy, queue, stop logic |
| `agents/QA_AND_SOURCES.md` | Evidence and truth checking |
| `DEPLOYMENT.md` | Cloud setup, permissions and provider interface |
| `src/core.py` | Persistent records, queue, QA, handoffs, export |
| `src/worker.py` | Cloud loop, Brave web search, evidence provider |
| `src/qa_orchestrator.py` | Real Codex reviewer calls |
| `src/role_supervisor.py` | Great Designer + executive role startup |
| `agent_control.mjs` | Official Codex SDK harness adapter |
| `bootstrap_checkpoint/` | Reconstructable local seed state, 0 strict QA approved |
| `e2vc_cloud_deployment_v5.xlsx` | Historical + queue + graph + QA status |

## Quick test (local tests only, not cloud agent execution)

```bash
python3 -m unittest discover -s tests -v
python3 src/cli.py bootstrap --data ./persistent_data
python3 src/cli.py status --data ./persistent_data
```

**Deployment depends on** connecting GitHub, Railway, and an authorized evidence provider. See `DEPLOYMENT.md`. The source is deliberately not designed to circumvent LinkedIn's access restrictions.