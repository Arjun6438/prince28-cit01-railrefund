# Capacity plan: RailRefund

Full corpus target: + commercial circulars and concession rules (1,500+ pages)

<!-- generated:start -->
## Measured on my start corpus (2026-11-25 09:32 UTC)

| What | Value |
|---|---|
| Files | 56 |
| Size on disk | 91.3 MB |
| Pages (PDF pages + 1 per 3,000 characters of text/HTML) | 356 |
| Characters of text | 197,987 |
| Estimated tokens (characters / 4) | 49,497 |
| Tokens per page | 139 |
| PDFs with almost no text (scanned? OCR is Day 7) | 32 |
| Files not measured (zip, xlsx, unreadable) | 0 |

## Estimate for the full corpus (target: 1,500 pages)

| What | How | Value |
|---|---|---|
| Scale factor | target / measured | x 4.2 |
| Tokens | measured tokens x scale | 0.21 M |
| Raw files | measured MB x scale | 385 MB |
| Chunks | tokens / 400 x 1.15 overlap | 600 |
| Embedding cost, one time | tokens x 1.15 x $0.15 per 1M | $0.04 = Rs 3 |
| Vector storage | chunks x 768 numbers x 4 bytes | 2 MB |
| Text in Postgres | characters x scale x 1.2 | 1 MB |

Assumptions: 4 characters per token, 400-token chunks, 768-dimension embeddings at $0.15 per 1M tokens, Rs 95 per US dollar.
<!-- generated:end -->

## What I conclude

1. Is embedding cost a problem for this product?
 No—using text-embedding-3-small costs $0.02 per 1 million tokens, so even 1 million tokens costs only about 2 cents
2. Do the vectors and text fit in my free Postgres?
If you're using Neon Free, the current limit is 1 GB per project; I cannot confirm your actual usage or whether the vectors and text fit until we measure them
3. Which number above am I least sure about, and how will I measure it better?
Actual vector and text storage—I will measure it after ingestion by checking chunk count, token count and database table sizes.
4. What changes if the corpus grows 10 times?
Embedding cost and vector storage will grow roughly 10×.
I'll monitor database usage, optimize chunking and reduce embedding dimensions if necessary.
