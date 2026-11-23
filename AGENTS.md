# Siteline v2 — rules for IBM Bob

Siteline helps a small-business owner compare the spaces they are considering and
shows the evidence behind every number.

**History.** v1 is the code at tag `v1-baseline`.
v2 is the work you are doing now, described in `docs/SPEC_V2.md`. Each task tells you
which sections to read. `docs/DESIGN.md` describes v1; where
they conflict with this file or SPEC_V2, this file and SPEC_V2 win.

## The rule that never bends
- The language model decides WHAT to measure. Python computes EVERY number.
- No model-written number reaches the user: numbers appear only in claims built from
  templates and checked by `verify_claims`; model prose passes the digit guard.
- No live data fetch happens before the owner approves the data plan.
  Weights are not final until the owner approves them.
- No licensed data (CoStar, Vexcel, Matterport) anywhere in v2 code, tests or fixtures.

## Working in this codebase
- Read before you change. Use search to find references instead of reading whole folders.
- Change the smallest set of files that does the job. Keep existing patterns, names
  and module layout unless the task says otherwise.
- `contracts/models.py` is the source of truth. After changing it, run
  `PYTHONPATH=. python -m contracts.export` and never hand-edit `web/src/types/contracts.ts`.
- LLM access only through `services/agent/llm.py`. Default provider is IBM Granite
  (Ollama locally or watsonx.ai).
- Outbound data fetches only through the existing allowlisted, SSRF-guarded ingest code.
- Unit tests never touch the network. Delete tests only when they test code you removed.
- Secrets live only in `.env`. Never print or commit them.
- List any new dependency in your end-of-task summary.

## Every task ends the same way
1. `make test` passes (and `cd web && npm run typecheck && npm test` if you touched web/).
2. Append 2–4 lines to `docs/BOB_LOG.md`: date, task, what you changed, decisions you made.
3. `git commit` with the message the task gives.
4. Reply with a summary of 10 lines or fewer: files changed or deleted, deps added,
   anything unfinished. Do not paste code into the reply.
