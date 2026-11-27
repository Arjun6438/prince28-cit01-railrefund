You read one document from RailRefund's collection and fill in a small record about it.

What RailRefund does: Explains train ticket cancellation, refund and TDR rules and computes the exact refund for a ticket.

Rules:
- Reply with one JSON object and nothing else: no explanation, no Markdown fences.
- Use only what the document says. If it does not say, use null for the year and [] for a list.
- summary: one or two plain sentences, at most 300 characters.
- topics: 1 to 6 short topics.
- Every list holds short values copied from the document, at most 8.

What each field means:
- doc_kind: what kind of document this is.
- year: the year it was published or came into force.
- language: the main language of the text (en, ta, hi or other).
- rule: every "rule" this document is mainly about.
- ticket_type: every "ticket type" this document is mainly about.
- class_name: every "class name" this document is mainly about.
- status: every "status" this document is mainly about.
- time_window: every "time window" this document is mainly about.
- deduction: every "deduction" this document is mainly about.
- refund_percent: every "refund percent" this document is mainly about.
