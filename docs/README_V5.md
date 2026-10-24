# e2vc Cloud Company Discovery — V5 (review branch)

Goal: 1,000 LinkedIn company pages with directly observed LinkedIn industry values.

**Deployment status:** NOT DEPLOYED. Railway and authorized LinkedIn evidence access are not connected. This branch contains architecture documentation, not a running cloud-worker implementation. Full runnable prototype is in the linked ChatGPT artifact bundle; source must be imported and reviewed before deployment.

## Data invariants
- Save each Similar pages card immediately: company name, displayed industry, location, actual LinkedIn URL when exposed, parent URL, source evidence, discovery ordinal. Never invent missing URLs.
- Expand Similar pages until the last expansion control disappears, when authorized browsing makes that possible. Flag partial expansion as unconfirmed.
- Enrich a company after opening its actual LinkedIn page: industry (required), headline, description, followers, specialties, size, website, headquarters, founding year (optional).
- Maintain append-only observations; no deleting already saved company records.
- Exact URL identity verification, source-backed field verification and parent-edge verification require three independent QA decisions before counting a company.
- Previously gathered 77 profiles are inherited historical rows, **not** independently certified. No company is counted as QA approved without actual reviewer receipts.
- P1: Tech / Venture & Investment / Finance; P2: unknown/review; P3: other. Equal P1 priority, FIFO within discovery depth; periodic lower-tier exploration.
- Completion only after 1,000 distinct LinkedIn URLs with raw LinkedIn industry and all three truth checker approvals. Blocked source means WAITING, never COMPLETE.

## Security and provider policies
Web search discovers LinkedIn company URLs. Authorized sources establish page facts and Similar pages evidence. Do not bypass authentication, rate limits, CAPTCHA, or prohibited automated scraping. No credentials, cookies, or research datasets should be committed to this public repository.

See docs/ARCHITECTURE.md, docs/AGENT_PROMPTS_FINAL.md, docs/DEPLOYMENT.md.
