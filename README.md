# CaseLens AI — Audit-Ready Case Management Portal & AI Investigator Copilot

A personal portfolio project: a compliance-investigation case management
portal where **every action is written to an append-only audit log**, with an
AI Investigator Copilot (arriving in Feature 2) that will summarize cases,
flag red flags, and draft disposition narratives — every AI suggestion
audit-logged alongside the human actions.

> **Synthetic data only.** Every person, account, transaction, and institution
> in this project is fictional and invented for demonstration. This is not
> employer work and describes no real customer or investigation.

## Status

Feature 1 of 8 is built — see [FEATURES.md](FEATURES.md) for the weekly roadmap.

**Feature 1 — Case API + append-only audit log**
- Case model: id, title, subject, alert type, status, risk level,
  timestamps, plus structured transactions and evidence notes
- Append-only audit log service: case created / viewed / status changed /
  note added each write an immutable entry (timestamp, actor, action,
  case id, details). There is no update or delete path for audit entries —
  by design, not by convention
- FastAPI app with an in-memory store and synthetic seed data (6 fictional cases)

## Run it (demo mode)

Demo mode is the default: the app runs fully offline with the seed data —
no API key, no external services, same pattern as my AI Code Review Agent.

```bash
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
.venv/bin/python -m case_portal          # http://127.0.0.1:8000
```

Start with an empty case queue instead of the seed data:

```bash
CASE_PORTAL_NO_SEED=1 .venv/bin/python -m case_portal
```

Run the tests:

```bash
.venv/bin/python -m pytest
```

## API (Feature 1)

| Method | Path | Description |
|---|---|---|
| GET | `/health` | Health check |
| GET | `/cases` | List cases; filter with `?status=` and `?risk_level=` |
| GET | `/cases/{id}` | Get one case (the view itself is audit-logged) |
| POST | `/cases` | Create a case (starts `OPEN`) |
| PATCH | `/cases/{id}/status` | Change status — validated transitions, `CLOSED` is terminal |
| POST | `/cases/{id}/notes` | Add an evidence note |
| GET | `/cases/{id}/audit` | The case's audit trail |
| GET | `/audit` | The global audit trail |

The actor recorded in the audit trail comes from the `X-Actor` header
(default `investigator.demo`).

Status flow: `OPEN → IN_REVIEW → ESCALATED → CLOSED`, with `OPEN → ESCALATED`
and `OPEN → CLOSED` also allowed; `ESCALATED` can step back to `IN_REVIEW`.
Invalid transitions return `409`.

Example:

```bash
curl -s http://127.0.0.1:8000/cases?status=OPEN
curl -s -X PATCH http://127.0.0.1:8000/cases/CASE-1001/status \
  -H 'Content-Type: application/json' -d '{"status": "IN_REVIEW"}'
curl -s http://127.0.0.1:8000/cases/CASE-1001/audit
```

## Roadmap

One feature per week — full detail in [FEATURES.md](FEATURES.md):

1. **F1** Scaffold + cases API + append-only audit log *(done)*
2. **F2** Investigator Copilot v1 — case summary + red-flag checklist, RAG over case documents with citations
3. **F3** Disposition narrative drafting with citations + investigator approve/edit flow, audit-logged
4. **F4** Document ingestion — upload/parse case documents, chunk + index for the copilot
5. **F5** Case workflow — assignments, SLA/escalation flags, search
6. **F6** React investigator UI — case queue, case detail, copilot panel, audit trail viewer
7. **F7** Copilot evaluation — LLM-as-a-judge scoring (demo fallback) + guardrail checks
8. **F8** Polish — worked example case in `examples/`, final test pass, README completion

## Stack

Python, FastAPI, Pydantic, pytest. LangGraph/LangChain and the RAG copilot
arrive with Feature 2.
