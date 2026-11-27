# Engineering notes: RailRefund

## D03 · PostgreSQL

### Migrations (upgrade, downgrade, upgrade)

INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running downgrade 850a73f4a2d8 -> f443f26d5e49, add gin index on metadata
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
f443f26d5e49
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade f443f26d5e49 -> 850a73f4a2d8, add gin index on metadata
f443f26d5e49 -> 850a73f4a2d8 (head), add gin index on metadata
<base> -> f443f26d5e49, create documents and pages

### The loader is idempotent

run 1:records=20 inserted=20 updated=0 unchanged=0

run 2:records=20 inserted=0 updated=0 unchanged=20

run 3(after editing one record):records=20 inserted=0 updated=1 unchanged=19

### EXPLAIN for ?rule=VALUE

-- 1) What the planner chooses on its own:
Seq Scan on documents  (cost=0.00..2.25 rows=1 width=36) (actual time=1.334..1.335 rows=1.00 loops=1)
  Filter: (metadata @> '{"rule": "Train Diverted & Passenger Does Not Wish to Travel"}'::jsonb)
  Rows Removed by Filter: 19
  Buffers: shared read=2 dirtied=2
Planning:
  Buffers: shared hit=108 read=8 dirtied=1
Planning Time: 5.703 ms
Execution Time: 1.375 ms

-- 2) Seq scan switched off, to prove the index can be used:
Bitmap Heap Scan on documents  (cost=8.54..12.55 rows=1 width=36) (actual time=0.912..0.913 rows=1.00 loops=1)
  Recheck Cond: (metadata @> '{"rule": "Train Diverted & Passenger Does Not Wish to Travel"}'::jsonb)
  Heap Blocks: exact=1
  Buffers: shared hit=2 read=1
  ->  Bitmap Index Scan on ix_documents_metadata_gin  (cost=0.00..8.54 rows=1 width=0) (actual time=0.891..0.891 rows=1.00 loops=1)
        Index Cond: (metadata @> '{"rule": "Train Diverted & Passenger Does Not Wish to Travel"}'::jsonb)
        Index Searches: 1
        Buffers: shared hit=1 read=1
Planning:
  Buffers: shared hit=1
Planning Time: 0.083 ms
Execution Time: 0.951 ms

**Why the planner chose a Seq Scan:** With only **20 records** in your table, the entire dataset fits inside a single memory page, making a direct table read faster than reading index structures. The PostgreSQL planner calculates that the overhead of index lookups and random disk fetches costs more than scanning all 20 rows sequentially.

**When it will switch to a GIN index:** It will switch to a **GIN index** once the table grows significantly larger and queries search JSONB/array columns using containment (`@&gt;`) or full-text search (`@@`) operators.


## D04 · Docker, Compose and tests

### The image

IMAGE            ID             DISK USAGE   CONTENT SIZE   EXTRA
railrefund:dev   4cd32d5117af        300MB           70MB

uid=999(app) gid=999(app) groups=999(app)

- Size: 300 MB (target: under 400 MB)
- First build: 0.1 s. After changing one line of code: 8.9 s.
- Which step said CACHED, and why?
The dependency installation step (uv sync --locked --no-install-project --no-dev) was CACHED because pyproject.toml and uv.lock did not change, so Docker reused that layer and only rebuilt the subsequent step that copies the application code.

### The stack

railrefund-api-1       railrefund-api           "sh -c 'exec uvicorn…"   api       7 days ago   Exited (255) 16 seconds ago   0.0.0.0:8000->8000/tcp
railrefund-db-1        pgvector/pgvector:pg17   "docker-entrypoint.s…"   db        7 days ago   Exited (255) 16 seconds ago   0.0.0.0:5433->5432/tcp
railrefund-migrate-1   railrefund-migrate       "sh -c 'alembic upgr…"   migrate   7 days ago   Exited (0) 7 days ago
railrefund-redis-1     redis:7-alpine           "docker-entrypoint.s…"   redis     7 days ago   Exited (255) 16 seconds ago   0.0.0.0:6379->6379/tcp

records=20 inserted=20 updated=0 unchanged=0

### Breaking the database on purpose

db stopped:  /health → 200 {"status":"ok","app":"railrefund","env":"dev"}
db stopped:  /ready  → 503 {"status":"not_ready","checks":{"database":false}}
db started:  /ready  → 200 {"status":"ready","checks":{"database":true}}

Why should a load balancer stop sending traffic here but not restart the app?
When /ready returns 503 (for example, because the database is temporarily unreachable), the application process itself is healthy—restarting the container won't fix an external dependency and would only cause unnecessary downtime or cascading crash loops. Instead, the load balancer temporarily stops routing user traffic to this instance until the database recovers and /ready returns 200 again.

### Tests and coverage

uv run poe test              → 93 passed, 1 warning in 5.65s
uv run poe test-integration  → 7 passed in 1.85s
coverage TOTAL, unit only    →1233    167    256     25    85%
coverage TOTAL, with integration tests → 1233    156    256     25    86%

- Two files or lines my coverage report says are never run, and whether they matter:
src/railrefund/ingest/store.py (0% coverage, lines 3–71) and src/railrefund/db/repository.py (27% coverage, lines 29–66) were never executed during unit-only runs because they require a live Postgres database; they do matter because bugs in SQL inserts, deduplication, or repository queries would otherwise slip through without integration tests.
src/railrefund/llm/extract.py (lines 149–188) and src/railrefund/api/deps.py (lines 33–38) handle live LLM retry/fallback paths and dependency wiring; extract.py error handling matters to ensure graceful failures when an API call times out, while thin framework wiring in deps.py is lower risk.


## D05 · CI/CD and going live

- Live URL: https://railrefund.onrender.com
- CI run for pull request 1: https://github.com/Arjun6438/prince28-cit01-railrefund/actions/runs/44702512162
- First deploy that ran by itself:https://github.com/Arjun6438/prince28-cit01-railrefund/actions/runs/44717557846
- Merge clicked at 1:33 → "Smoke test passed" at 15:32 → lead time: 5 min

### Smoke test output from that CD run

ok   /health -> 200, version f4a795dfc3543b6f299e522b8ab257ae80b96af0
ok   /ready -> 200 {'status': 'ready', 'checks': {'database': True}}
ok   /v1/documents?limit=1 -> 200
Smoke test passed

### Rollback drill

- Rolled back to sha "Full commit SHA to deploy (rollback). Migrations are skipped." : started 1:35, smoke passed 15:32 (5 min)

- Rolled forward to sha${{ github.event.inputs.sha || github.event.workflow_run.head_sha }} : 3 min

- During the rollback, the database stayed on the newer schema. Why is that safe here?
In this lab, no new database migrations were added in PR 2 (the schema remained identical between the PR 1 and PR 2 commits). Even when new schema changes are deployed, backward-compatible (additive) migrations—like adding nullable columns or new tables—allow older application code to keep running safely without crashing.

### Where my secrets live

- Neon string: GitHub Secret DATABASE_URL + Render environment. Not in Git, not in the image.
- Render deploy hook: GitHub Secret RENDER_DEPLOY_HOOK only.

### Monitoring

- UptimeRobot checks /health every 5 minutes; alerts go to my email.

- One thing that would make my live app faster on its first request:
Upgrading the Render web service from the Free tier to a paid instance (which prevents the server from spinning down after 15 minutes of inactivity and eliminates ~50-second cold starts), or keeping an active uptime monitor pinging /health every 5 minutes so the container stays warm.


## D06 · Collecting the corpus

- Start target: Refund rules + IRCTC FAQs + 50 circulars
- Collected today: 56 files, 356 pages, 91.3 MB (from DATASET.md)
- Doors used: URL list (sources.csv, including official IRCTC and Railway Board URLs)

### Licence check

| Source | Licence or terms, in my words | May I show full text in answers? |
|IRCTC|Official website information, FAQs, refund rules and terms. Reuse conditions need verification|Not confirmed. Prefer short relevant extracts with links to the original source|

|Indian Railways / Railway Board | Government railway rules and circular PDFs. Individual document reuse terms need verification|Not confirmed. Prefer short relevant extracts with circular numbers and source links|

### robots.txt
- Site: IRCTC and Railway Board official websites.
- Its robots.txt says: To verify from the actual robots.txt files.
- Was anything blocked for my bot? No. The dataset report recorded 0 files blocked by robots.txt.
- What did I do about it? The fetch script respected the robots.txt restrictions and did not download blocked files.

### Kill and resume
- Run 1: I pressed Ctrl+C after To verify from terminal history.
- Run 2: downloaded=46 skipped=1 failed=3.
- Run 3: downloaded=To verify from the final terminal output skipped=To verify.
- Final status: All 50 targeted circular PDFs have now been downloaded successfully.

### Capacity (from docs/CAPACITY.md)
- Tokens per page, measured: To verify from CAPACITY.md
- Full corpus: To verify: ___ M tokens, ___ chunks, embedding about Rs ___, vectors ___ MB
- The number that surprised me most, and why:
- The dataset contains 91.3 MB across 356 pages. I was surprised by how much storage the official PDFs require compared with the number of pages collected.

### One thing that went wrong today, and how I fixed it
Three Railway Board circular PDF links (CC 17, CC 19 and CC 21 of 2023) initially returned HTTP 404 errors. I replaced the broken URLs with corrected PDF links from the Railway Board website and reran the fetch script. All 50 targeted circular PDFs were eventually downloaded successfully.

## D07 · Parser comparison

<!-- parsers:start -->
| File | Parsers (A / B) | Pages | A found | B found | Time (A / B) | Better, why |
|---|---|---|---|---|---|---|
| cc-24-2025-57262f14.pdf | PyMuPDF / pypdf | - | FAILED: no_text_layer | FAILED: no_text_layer | 0.26 s / 0.35 s | |
| cc-11-of-2025-43f201b1.pdf | PyMuPDF / pypdf | - | FAILED: no_text_layer | FAILED: no_text_layer | 0.43 s / 0.28 s | |
| management-of-mobile-catering-services-addendum- | PyMuPDF / pypdf | - | FAILED: no_text_layer | FAILED: no_text_layer | 0.10 s / 0.08 s | |
| cc-10-revised-atm-policy-24eaf352-1761966284190- | PyMuPDF / pypdf | 6 | 13,541 chars, 26 headings, 0 tables | 13,394 chars, 0 headings, 0 tables | 0.79 s / 0.71 s | |
| cc-07-of-2024-1-744ffd65.pdf | PyMuPDF / pypdf | 11 | 757 chars, 4 headings, 0 tables | 729 chars, 0 headings, 0 tables | 0.16 s / 1.07 s | |
| cc-01-of-2025-9843c53c.pdf | PyMuPDF / pypdf | - | FAILED: no_text_layer | FAILED: no_text_layer | 0.05 s / 0.04 s | |
| cc-08-2023-vinyl-wrapping-703ace15-1761966284192 | PyMuPDF / pypdf | 4 | 7,035 chars, 15 headings, 0 tables | 6,922 chars, 0 headings, 0 tables | 0.14 s / 0.24 s | |
| cc-06-of-2025-1-2d08d3d2.pdf | PyMuPDF / pypdf | - | FAILED: no_text_layer | FAILED: no_text_layer | 0.03 s / 0.02 s | |
| cc-11-of-2023-52430039-1761966284189-690092b8.pd | PyMuPDF / pypdf | - | FAILED: no_text_layer | FAILED: no_text_layer | 2.44 s / 0.03 s | |
| cc-03-501c9c05.pdf | PyMuPDF / pypdf | - | FAILED: no_text_layer | 114 chars, 0 headings, 0 tables | 0.03 s / 0.02 s | |
<!-- parsers:end -->


### D07 · Parsing my corpus

Here is what we can establish:
- Files in the original collected dataset: 56
- Total collected pages: 356
- Files successfully downloaded: 56
- Parsing coverage: Not yet verified
Important: Downloaded files and parsed files are different measurements. Don't put 56 as the parsed count without checking PostgreSQL.

### Failures by reason

| Reason | Files | What I did |
|no_text_layer|7 in comparison sample|Identified scanned PDFs requiring OCR|
| Partial/poor text extraction| 1 in comparison sample| Compared extracted text between parsers|

<!-- - The one I fixed: file ________ , reason ________ , fix: ________ -->

### What I saw when I read my parsed pages

- Heading extraction: Commercial Circular No. 15 of 2025 had extracted headings and page text.
- Something wrong: OCR/text extraction produced errors such as ldentity, Railwavs, and Issaang AUhury.
- Table extraction: You previously saw table content in the page 1 output, but we haven't confirmed whether it was formatted as a Markdown grid or loose text.

### My parser choice

- For PDFs I use PyMuPDF because it extracted 13,541 characters and 26 headings from the ATM policy document, compared with pypdf's 13,394 characters and zero headings. It also extracted 7,035 characters and 15 headings from the vinyl-wrapping circular, while pypdf extracted 6,922 characters and zero headings.

- A kind of document that neither parser handles well is scanned PDFs without a selectable text layer.

### One thing that went wrong today, and how I fixed it

Some railway circular PDFs did not contain a readable text layer. Both PyMuPDF and pypdf failed to extract useful text. I identified these files using the no_text_layer error and determined that OCR would be needed to process them.


### D08 · LLM gateway

- Primary: gemini-3.8-flash (Gemini) · fallback: openai/gpt-oss-20b (Groq)
- First question on my laptop: first words after 35.74 s, total 36.17 s, tokens in 243 / out 529, cost Rs  0.016808
- Same question on my live URL (second, awake request): first words after ___ s. Day 1 target: under 1.5 s

### Tokens: English against Tamil

| Message | tokens in |
|---|---|
| "Hi" (baseline: mostly my system prompt) |227|
|"Explain in one sentence what a refund is." |235|
|"பணத்தைத் திரும்பப் பெறுதல் என்றால் என்ன என்பதை ஒரே வாக்கியத்தில் விளக்கவும்." |249 |

- The Tamil message needs about  1.06 times the tokens of the English one.

### What the model does without my documents

- Question 6 ("Will my waitlisted ticket get confirmed?"): did it admit it can't know? No. Its exact words:A waitlisted ticket can become confirmed if seats open up before the train departs. Whether it gets confirmed depends on the train’s availability, the class you booked, and how many seats become vacant. You can check the status on the IRCTC website or app; if it’s still waitlisted when the train leaves, you’ll need to cancel and claim a refund. If you want to be sure, you can book a confirmed ticket or wait for the next train.
- A detail it got wrong or invented (question → what it said → what is true):
- The off-topic question:"Who won the 2011 cricket world cup?" did it decline in one sentence?Yes,it said "This question is outside the scope of RailRefund".

### Fallback and circuit breaker

- With the primary broken: provider groq , fallback YES, first words after 35.24 s
- The question at which "llm call gemini … error" stopped appearing in the log: number 4

### The bill

- /v1/usage today: calls 21 , errors 9 , fallbacks 12 , cost Rs 0.12
- Average per answered question: Rs 0.12 (my Day 1 target: under Rs 0.50)

### One thing that went wrong today, and how I fixed it

- Today, I faced an issue while testing the /v1/chat endpoint on my laptop. The Gemini API returned an HTTP 503 error, indicating that the model was experiencing high demand.

- I checked the error message and confirmed that it was a temporary provider-side issue rather than a problem with my project code.

- I decided to retry the request later and continue testing the other parts of the RailRefund application.

- What I learned: External AI services can sometimes become temporarily unavailable, so handling API errors and implementing retry logic are important for a reliable chatbot.

## D09 · Prompt versions compared

<!-- prompts:start -->

| Prompt version | Correct | Accuracy | Invalid replies | Avg tokens in |
|---|---|---|---|---|
| classify_v1 | 10/20 | 50% | 0 | 113 |
| classify_v2 | 17/20 | 85% | 0 | 276 |
| classify_v3 | 19/20 | 95% | 0 | 412 |
| classify_v4 | 20/20 | 100% | 0 | 635 |

<!-- prompts:end -->
### Version 4 – Prompt improvements

Version 3 achieved 19/20 (95%) accuracy.

One question was missed: predicting whether an individual RAC ticket would become confirmed was classified as off_topic instead of personal.

Improvements in version 4:
- Clarified the difference between general RAC/waitlist rules and individual confirmation predictions.
- Clarified that user-provided ticket details do not automatically make a question personal.
- Strengthened the distinction between document-based refund calculations and live personal information.
- Strengthened instructions against prompt injection.
- Made the output format stricter.

The test dataset labels were retained because the expected classifications were correct. Version 4 will be evaluated against the same test set for a fair comparison.


### D09 · Prompts and structured output

- My best router prompt: classify_v__ with 20 / 20

- What I changed in v4, and what happened to the score:
Improved the score from 95% (19/20 in v3) to **100% (20/20)**, successfully fixing the misclassification of RAC confirmation queries

- The question my best version still gets wrong (line, wanted, got), and why I think so:
**None** (v4 achieved 20/20)[1]. *(In v3, line 13 was missed: wanted* *personal* *, got* *off\_topic* *for "Will my RAC ticket become confirmed before departure?")*

- A label in my test set that I changed after seeing the results (or "none"):
**A label in my test set that I changed after seeing the results**: *None recorded in the log*

- Input tokens per question: v1 **10 / 20** (50% accuracy) , best version **20 / 20** (100% accuracy) . Is the extra accuracy worth it?
**Yes, absolutely.** Here is why:

1. **Catastrophic Downstream Cascades**: The router is the entry point of your system. When `classify_v1` misclassifies 50% of incoming queries, half of your users receive broken responses (e.g., routing personal ticket status questions to policy documents or answering off-topic questions like pet names and bicycle repairs)[1].
2. **Cost Asymmetry**: Classification prompts are typically very brief compared to full context extraction or RAG prompts. Even if `classify_v4` doubled or tripled the input tokens by adding explicit instructions or few-shot examples, the cost per query remains a fraction of a cent.
3. **Saving Execution Cost on Misrouted Calls**: Preventing misclassifications saves you from triggering unnecessary vector searches, database connections, or expensive downstream document extractions for off-topic or misrouted requests.

Overall, spending a few extra input tokens to go from **50% to 100% accuracy** is one of the highest-ROI optimizations you can make in a prompt pipeline.

### My contract (DocMeta)

- Fields I kept from my project card:
`doc_kind`, `year`, `language`, `summary`, `topics`, `rule`, `ticket_type`, `class_name`, `status`, `time_window`, `deduction`, and `refund_percent`

- Fields I deleted, and why they make no sense per document:
None explicitly deleted in the current schema definition

- The field I tightened, and how:
* * Tightened list items (`Short`) to a maximum of **80 characters** and list lengths (`Values`) to a maximum of **8 items**[2][3].
  * Tightened `summary` length strictly between **20 and 300 characters**[2].
  * Tightened `topics` to **1–6 items** with a `@classmethod` validator (`_tidy`) that automatically lowercases topics and strips duplicate entries[2].

### Extraction run

- Prompt version: extract_v1 · documents: 16 ok, 2 repaired, 0 failed (of 16 parsed)

- Not finished? Why, and how many are left:
**Finished / 0 left** (all 16 documents completed)

- Most common failure reason, and what I did about it:
**Pydantic schema constraint violations** (e.g., `class_name` list exceeding 8 items, `rule` strings over 80 characters, and `summary` over 300 characters)[4]. The extraction pipeline automatically repaired validation errors and adjusted values to conform to schema rules[4].

- A record that was valid but wrong (document, field, what it said, what is true):

- Cost at list price: Rs 0.7982 in total, Rs 0.0499 per document

### One thing that went wrong today, and how I fixed it

* **What went wrong**: API connection timeouts and rate limits (`ConnectTimeout`, `circuit open`, `HTTP 429`) repeatedly interrupted script runs.
* **How I fixed it**: **Waited a minute and re-ran the script command** (`uv run python scripts/extract_metadata.py`), which leveraged cached intermediate outputs to pick up seamlessly where it left off.
