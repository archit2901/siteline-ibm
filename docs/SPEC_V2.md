# Siteline v2 — change spec

v1 (tag `v1-baseline`) is the starting point. This file lists **only what
changes**. Anything not mentioned stays as it is. Read the relevant code before
assuming how it works.

---

## §1 Product (v2 framing)

Siteline tells a small-business owner which of the spaces **they are considering**
would win the most customers, and shows the evidence behind every number. The owner
leaves with a ranked comparison and a **verified site memo** to hand to a lender,
landlord or partner.

Challenge fit: Decision Intelligence and Entrepreneurship. AI is the core (agents
decide what to measure per business); people stay in control (two approvals, every
number computed and verified by code).

## §2 What stays exactly as in v1

- The rule: the model decides what to measure, Python computes every number;
  claim templates plus `verify_claims`.
- The two approval checkpoints (data plan, weights) and the live event stream.
- Intake, profiler, factor analyst, dataset scouts, critic and concierge roles.
- H3 grid, Huff model, weights, rescore, stability, community need.
- Houston region and its public data (Census ACS, LODES, OpenStreetMap, CMS, CDC PLACES).
- Allowlisted, SSRF-guarded ingestion; rate limits; recorded-run replay.

---

## §3 Change A — IBM Granite as the model provider

- In `services/agent/llm.py`, add two providers and make Granite the default:
  - `ollama`: `langchain-ollama` `ChatOllama`, env `OLLAMA_BASE_URL`,
    `OLLAMA_MODEL_SMART`, `OLLAMA_MODEL_FAST`.
  - `watsonx`: `langchain-ibm` `ChatWatsonx`, env `WATSONX_URL`, `WATSONX_APIKEY`,
    `WATSONX_PROJECT_ID`, `WATSONX_MODEL_SMART`, `WATSONX_MODEL_FAST`.
  - `LLM_PROVIDER` default becomes `ollama`. Keep per-role overrides
    (`LLM_PROVIDER_<ROLE>`, `MODEL_<ROLE>`).
  - Existing Bedrock, Gemini and OpenRouter providers may remain as optional
    development providers but are never the default. Their packages become optional.
- Cost table: Ollama calls cost 0. watsonx prices come from optional env
  `WATSONX_PRICE_IN_PER_M` / `WATSONX_PRICE_OUT_PER_M` (default 0, shown as "not priced").
- Structured output: prefer JSON-schema structured output over tool calling for every
  role. Keep all existing Pydantic validation and Python clamping.
- `AGENT_MODE` default stays `scripted`. `planned` mode (deepagents, tool calling)
  stays optional and is not required to work on Granite.
- Replace `scripts/check_bedrock.py` with a provider-agnostic `scripts/check_llm.py`:
  one structured call per tier, printing provider, model id, latency and pass/fail.
- UI: the run meter and developer view label the model as IBM Granite (from `/health`).

## §4 Change B — remove licensed and sponsor-only layers

Remove completely (code, tests, fixtures, UI, docs references):
- The site inspector (`services/agent/inspector.py`, its tests, its pipeline stop and
  events) and everything Vexcel or Matterport (including `web/src/lib/media.ts` uses,
  3D tour links, parked-car counts).
- CoStar listing search in `services/agent/tools/sites_client.py` (the CoStar paths of
  `LocalSites` and `HttpSites`), the `services/sites/` placeholder, and any code reading
  `data/raw/houston_costar_*`.
- The Photon iMessage service (`services/photon/` and its tests).
- In `contracts/models.py`: remove `SiteInspection` and CoStar-only fields on
  `SiteCandidate`, `RankedSite` and `Recommendation` (listing ids, listed parking,
  lease status, Matterport ids, floor data that only came from listings). Regenerate types.
- Pipeline: drop the inspector stop; the site-search stop becomes "owner sites" (§5).
  Update stage names in the web run line and developer view.
- Done when `grep -riE "costar|vexcel|matterport|photon" services web/src contracts scripts`
  returns nothing.

## §5 Change C — owner-supplied candidate sites

- Intake collects **2–5 candidate sites**: address (required), label (optional),
  monthly rent in USD (optional), size in sq ft (optional). Existing locations keep
  working as in v1. Chat intake asks for candidates if missing; the form view has rows
  for them. Add the fields to the intake/run contracts.
- New `OwnerSites` class implementing the existing `SitesClient` protocol in
  `sites_client.py`: geocodes each address with the Census Geocoder
  (`onelineaddress`, benchmark `Public_AR_Current`) through the allowlisted fetch with
  caching, rejects addresses outside Harris, Fort Bend and Montgomery counties with a
  clear message, and returns `SiteCandidate`s. `MockSites` stays for fixtures (with
  invented addresses and coordinates).
- Geocoding is a live fetch, so it runs **after** data-plan approval. The data plan
  always lists "Census Geocoder: locate your candidate addresses" as a required item.
- Ranking: rank **all** candidates with the existing Huff, factor and rent-per-customer
  logic. Rent per customer uses the owner's monthly rent × 12. Candidates without rent
  are ranked without the cost term, as v1 does for missing rent.
- Claims: remove listing-sourced templates; add a template stating that rent figures
  are the owner's inputs. The concierge answers "which is cheapest per customer?",
  "compare A and B" and "why is X first?" over the owner's candidates.

## §6 Change D — verified site memo

- New `services/agent/memo.py` building a memo from the verified report with sections:
  Summary, Recommendation, Why this site, Risks and limits, Next steps.
- Narrative comes from a new Granite role `memo_writer` (smart tier). It may reference
  claims only as `{{claim:ID}}`; Python substitutes the verified claim text.
- Digit guard (new `services/agent/guard.py`): reject any digit sequence, or number word
  (zero–twenty, tens, hundred, thousand, million, dozen, half, percent), outside
  `{{claim:ID}}` placeholders unless it appears verbatim in the owner's own inputs.
  Unknown claim IDs also fail. Up to 2 retries, then a template-only memo built from claims.
- Outputs `memo.md` and `memo.html` (print stylesheet so the owner can save a PDF),
  served through the existing artifacts route. Footer: data sources, limits, and
  "Numbers computed and verified by Siteline's engine; narrative drafted by IBM Granite."
- The pipeline writes the memo after the critic and emits the same file event v1 uses
  for other artifacts.

## §7 Change E — evals, recordings, docs

- Run the factor-analyst evals on Granite (judge role on Granite too). Save results to
  `evals/results/<date>.json`. Fix prompts only where Granite fails a check.
- Delete old golden runs (they contain licensed listing data). Record new runs live on
  Granite with owner candidates for the laundromat and tutoring-center presets.
- README: update the diagram and agents table (Granite); remove the event line at the
  top and the sponsor-track, CoStar, Vexcel, Matterport and Photon sections; replace
  "How AI was used" with a "How IBM Bob was used" section that describes the v2 changes
  in this spec, summarised from `docs/BOB_LOG.md`; keep a Contributors section listing
  everyone who built v1.

## §8 Optional Change F — Boston region (only if coins remain)

Add region `boston` (Suffolk 25025, Middlesex 25017, Norfolk 25021) to the stock-grid
build and catalog using the same public sources; select region by `REGION_ID`.

## §9 Done for v2

`make test`, web typecheck and tests pass; a live Granite run with owner candidates
finishes verified; the licensed-data grep in §4 is empty; the memo downloads as md and html.
