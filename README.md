# RailRefund

Explains train ticket cancellation, refund and TDR rules and computes the exact refund for a ticket.

**Status:** Day 1 foundation (FastAPI skeleton, quality gates). Live URL from Day 5.

## Who it's for

Chennai student who travels home to Tirunelveli every month and keeps losing money on waitlisted and Tatkal cancellations.

## The problem

Train ticket refunds are notoriously complex because the payout depends on a shifting combination of your travel class, booking type, and exact cancellation timing. Deductions vary drastically depending on whether a ticket is confirmed, RAC, or waitlisted, while confirmed Tatkal bookings generally yield no refund at all upon voluntary cancellation. Additionally, cancelling close to the departure time triggers steep percentage-based penalties, and partial cancellations on mixed-status group tickets apply completely different rules to each passenger. Because so many variables overlap, passengers often find it nearly impossible to predict the exact amount they will get back

## Data sources

Railway Passengers (Cancellation of Ticket and Refund of Fare) Rules 2015, IRCTC cancellation and refund pages, TDR rules, Railway Board commercial circulars, concession rules.

- https://contents.irctc.co.in/en/eticketCancel.html
- https://www.indianrail.gov.in/
- https://indianrailways.gov.in/
- Terms: Public railway rules. Cite rule or circular number.

## Hardest problem (my interview story)

Refund computation: the answer depends on class, status, Tatkal, time before departure and partial cancellation, so it must come from a rules engine in code with the rule cited, not from the LLM.

## Signature feature

Refund simulator: choose class, status and hours before departure and see the exact refund and the rule behind it.

## Target metric

Refund amount exact for 100% of 40 test tickets.

## Run it locally

```bash
uv sync
cp .env.example .env   # Windows: copy .env.example .env
uv run uvicorn railrefund.api.main:create_app --factory --reload
```

Then open http://localhost:8000/docs

## Checks

```bash
uv run pytest
uv run ruff check .
```
### Why I changed these targets
* **Speed (answers):** Lowered from 4 s (1.5 s first token) to 3 s (1 s first token) because railway refund circulars are compact, allowing fast top-3 chunk retrieval and snappy responses on mobile networks.
* **Accuracy:** Increased the golden set from 40 to 60 test tickets to cover all combinations of Indian Railways classes, quotas (CNF, RAC, WL, Tatkal), and cancellation time windows without calculation errors.
* **Cost:** Lowered from Rs 0.50 to Rs 0.25 per question because deterministic refund math is offloaded to local code, keeping LLM context windows small and inexpensive.

## Run locally in one command

You need Docker Desktop running. From a fresh clone:

```bash
docker compose up --build
```

This starts Postgres (with pgvector), Redis, runs the migrations and loads the sample data once, then starts the API.

- API docs: http://localhost:8000/docs
- Liveness: http://localhost:8000/health
- Readiness (checks the database): http://localhost:8000/ready

Stop with `Ctrl+C`, then `docker compose down`. Add `-v` to also delete the database volume.

## Develop and test

| Task | Mac / Linux | Windows |
|---|---|---|
| Run the API with reload | `make dev` | `uv run poe dev` |
| Unit tests (no network) | `make test` | `uv run poe test` |
| Integration tests (needs `docker compose up`) | `make test-integration` | `uv run poe test-integration` |
| Coverage report | `make cov` | `uv run poe cov` |
| Lint and format check | `make lint` | `uv run poe lint` |
