# CaseLens AI — Feature Roadmap (one feature per week)

| # | Feature | Status |
|---|---|---|
| F1 | Scaffold + cases API + append-only audit log | ✅ Done — 2026-11-26 |
| F2 | Investigator Copilot v1 — case summary + red-flag checklist, RAG over case documents with citations | Planned |
| F3 | Disposition narrative drafting with citations + investigator approve/edit flow, audit-logged | Planned |
| F4 | Document ingestion — upload/parse case documents, chunk + index for the copilot | Planned |
| F5 | Case workflow — assignments, SLA/escalation flags, search | Planned |
| F6 | React investigator UI — case queue, case detail, copilot panel, audit trail viewer | Planned |
| F7 | Copilot evaluation — LLM-as-a-judge scoring (demo fallback) + guardrail checks | Planned |
| F8 | Polish — worked example case in examples/, final test pass, README completion | Planned |

## F1 — what shipped (2026-11-26)

- Python package layout (`case_portal/`), requirements, `.gitignore`, `.env.example`
- Case domain model (status / risk / alert-type enums, structured transactions
  and evidence notes) with 6 clearly synthetic seed cases
- Append-only audit log service — create / view / status-change / note actions
  all write immutable entries; no update or delete path exists, and reads
  return copies so stored history cannot be tampered with through the API
- FastAPI endpoints: list cases with status/risk filters, get case, create
  case, change status (validated transitions, CLOSED terminal), add note,
  per-case audit trail, global audit trail
- Demo mode: runs fully offline on seed data, no API key
- 22 pytest tests covering CRUD, filters, status transitions, and the
  audit log's append-only guarantees — all passing
