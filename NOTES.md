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

```text
paste the output of: docker images railrefund:dev
paste the output of: docker run --rm railrefund:dev id
```

- Size: ___ MB (target: under 400 MB)
- First build: ___ s. After changing one line of code: ___ s.
- Which step said CACHED, and why? (1 line, your own words)

### The stack

```text
paste the output of: docker compose ps -a
paste the last line of: docker compose logs migrate   (first up, then after down + up)
```

### Breaking the database on purpose

```text
db stopped:  /health → paste
db stopped:  /ready  → paste
db started:  /ready  → paste
```

Why should a load balancer stop sending traffic here but not restart the app? (2 lines)

### Tests and coverage

```text
uv run poe test              → paste the last line
uv run poe test-integration  → paste the last line
coverage TOTAL, unit only    → paste
coverage TOTAL, with integration tests → paste
```

Two files or lines my coverage report says are never run, and whether they matter: (2 lines)


## D05 · CI/CD and going live

- Live URL: (paste)
- CI run for pull request 1: https://github.com/Arjun6438/prince28-cit01-railrefund/actions (paste the run link)
- First deploy that ran by itself: (paste the CD run link)
- Merge clicked at __:__ → "Smoke test passed" at __:__ → lead time: ___ min

### Smoke test output from that CD run

```text
paste the last 4 lines of the "Smoke test the live URL" step
```

### Rollback drill

- Rolled back to sha ________ : started __:__, smoke passed __:__ (___ min)
- Rolled forward to sha ________ : ___ min
- During the rollback, the database stayed on the newer schema. Why is that safe here? (2 lines)

### Where my secrets live

- Neon string: GitHub Secret DATABASE_URL + Render environment. Not in Git, not in the image.
- Render deploy hook: GitHub Secret RENDER_DEPLOY_HOOK only.

### Monitoring

- UptimeRobot checks /health every 5 minutes; alerts go to my email.
- One thing that would make my live app faster on its first request: (1 line)
