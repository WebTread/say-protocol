# Say

**Name locked by Captain Grant, 2026-08-11: Say.** Formal cites may say “the Say protocol.”  
**Version:** 0 (draft for hardening + model ratification)  
**Date:** 2026-08-11  
**Status:** DRAFT — not an adopted web standard until gates in §19 clear.  
**License (proposed):** CC BY 4.0 for the text · Apache-2.0 for reference code · see §18  

**Authors (crews, not sessions):**  
Sledge (Grok) and Beacon (Claude), agent crews of the SayVel yard, under direction of Grant.  
Its primary technical authors are AI agent crews, credited as such, with a human Captain accountable for publication. Whether that is a first is for others to say; the drafting record publishes either way.

**Product split:** The open protocol is vendor-neutral. **Agent Doors** is a separate commercial product that implements it. Competitors are expected and welcome to implement the protocol without using that product.

---

## Abstract

Agents waste enormous effort discovering that a business **cannot** help: wrong area, wrong service, closed, no emergency desk. Humans hit the same wall with prettier HTML.

**Say** is a small, vendor-neutral contract a website can implement so a machine can ask one question of **that one site**:

> Can *you* take this job, here, under these constraints?

and get a cheap, structured, honest answer: **yes**, **no**, **indeterminate**, or **safety** — with freshness, parity to the human site, and optional receipts on consequential yes paths.

It is **not** a directory. It is **not** a chatbot. It is **not** a marketplace of “trusted network” providers. Each origin speaks for itself.

---

## 1. Motivation (non-normative)

Today an agent that needs a water heater repaired must scrape marketing pages, guess service areas, and burn tokens on layout chrome. A clean refusal should be the cheapest byte on the site. A qualified yes should carry enough structure to act without a second novel of HTML.

Publisher-only conventions without callers rot (see the empty-file problem around `llms.txt`-style artifacts). This protocol is designed to ship with:

1. a **consumer** path (agents that actually ask),  
2. a **validator** (conformance walks),  
3. a **scoreboard** (measured cold decision cost),  
not merely a file format for badges.

---

## 2. Design principles

1. **One site, one voice.** The answer surface is scoped to a single business origin. Multi-tenant “find all providers” APIs are out of scope.
2. **Meter depth, not the brake light.** Basic scope answers stay free. Paid tiers may change rate or deeper tools later; they must not hide whether the business serves the job at all.
3. **Parity.** Nothing is asserted to a machine that a human could not obtain by asking the business through ordinary means.
4. **Fail closed.** An unconfigured surface returns a clean failure, not a partial or misleading yes.
5. **No self-attested magic.** Capacity theater, “AI ready” badges, and self-minted trust scores are non-conformant as protocol claims.
6. **Human consent stays human** where law and decency require it. The protocol does not authorize fully machine-formed consumer commitments in regulated home-service contexts.
7. **Non-harm.** Implementations must not use the protocol to coerce, deceive, or build prohibited discrimination machinery (see §12).
8. **Win for principal, business, and agent.** Extractive dark patterns fail conformance intent even if they parse.

---

## 3. Conformance levels

| Level | Name | Requirement |
|-------|------|-------------|
| **L0** | Structured facts | Public business facts available as structured data (JSON-LD and/or a flat facts document): identity, NAP, hours, services, area at stated grain |
| **L1** | Brake light | Discovery + descriptor + answer operation; honest yes/no/indeterminate/safety |
| **L2** | Records | Consequential yes paths can mint an idempotent record (receipt); unsigned allowed in v0 |
| **L3** | Living surface | Change feed (fact *class* changed + `as_of`) and honest freshness |

A deployment MAY claim only levels it passes on a public validator. Self-badge claims without a validator profile are non-conformant marketing, not protocol failure of the wire format.

---

## 4. Terminology

- **Origin:** the site answering for one business.  
- **Descriptor:** machine document describing the answer surface.  
- **Ask:** one scope/availability question.  
- **Record (receipt):** durable reference to a consequential answer.  
- **Brake light:** the cheap L1 answer path.  
- **Preflight:** lowercase *operation* name for the ask (not the protocol title; avoids CORS confusion).  

---

## 5. Discovery

### 5.1 Primary

Successful responses for the human site’s HTML SHOULD include:

```http
Link: </agent/v0/index.json>; rel="service-desc"; type="application/json"
```

HTML documents SHOULD also include:

```html
<link rel="service-desc" href="/agent/v0/index.json" type="application/json">
```

`rel="service-desc"` is registered (RFC 8631). This specification does **not** mint a new well-known URI in v0.

### 5.2 Paths

Example layout (informative; discovery is authoritative):

```
/agent/v0/index.json          descriptor
/agent/v0/preflight           ask (GET primary; POST allowed)
/agent/v0/facts.json          derived public facts (optional L0/L1)
/agent/v0/changes.json        change feed (L3)
/agent/v0/r/<id>              record fetch (L2)
```

Version appears in the path. v0 allows breaking changes.

---

## 6. Descriptor (normative sketch)

JSON object including at minimum:

| Field | Meaning |
|-------|---------|
| `v` | protocol version (0) |
| `as_of` | date of oldest material fact the surface will rely on |
| `jobs` | closed list of job tokens *this site* understands; each MAY declare a per-job `geo` mode (`"grain"` = answered by the declared grain, `"arranged"` = geography genuinely settled case by case) |
| `grain` | the site's OWN declared geographic decision vocabulary (regions, cities, zips, radius classes — whatever the business actually decides by; there is no global closed enum, because real businesses do not decide by a spec's list) |
| `endpoints` | URLs for ask, facts, changes, complaint as implemented |
| `auth` | what is required for L1 (v0: none for basic ask) |
| `rate` | the implementation's DECLARED rate posture, including honestly declaring `"none"` (shared hosting cannot always meter; a declared absence beats a fictional limit) |
| `safety` | how life-safety classes are handled |
| `valid_until` | a date after which consumers MUST treat this surface's artifacts as ABSENT, not merely stale — and past which a live origin SHOULD go dark server-side too (the reference implementation 404s itself on expiry; an abandoned install must not serve dead facts forever) |

**Forbidden in v0 descriptor claims:** self-attested capacity counts presented as protocol truth; “network trusted” badges; prices framed as firm offers unless a later profile defines offer objects; credential facts (license, insurance) without a pointer to the issuing authority’s own check surface — no check URL, no claim.

---

## 7. The ask

### 7.1 Request (conceptual)

Required:

- `job` — token from the descriptor’s vocabulary  

Common optional:

- `where` — geography expressed at the site's declared grain (the param is grain-neutral by design: a region business answers regions, a zip business answers zips)  
- `urgency` — e.g. `emergency` \| `same_day` \| `this_week` \| `schedule`  
- `need` — an array of strings drawn from the descriptor's declared `need` vocabulary (for example `["min","fee_policy","open_now","license_ref"]`); unknown members are ignored and named back in the response so divergent implementations cannot silently split  
- `agent.purpose` — `prequalify` \| `compare` \| `book_intent` \| `research` \| `audit`  

Unknown `job` → error naming the allowed enum (teach in one round trip).

**Geography is grain-locked to the site's own decision grain.** v0 does not take arbitrary lat/lon points, and a site MUST NOT answer at a finer geographic grain than the grain on which it actually decides service (if it decides by city, it answers by city, even when asked by zip). Every geographic `no` MUST carry a `basis` naming the operational reason (distance, licensing territory, crew reach, seasonal). Four independent reviewing models converged on the same hazard: quantization changes resolution, not the redlining risk — enumeration of coarse answers can still draw a coverage map. So the protocol's defenses are stacked, not singular: grain-locking here, the `basis` requirement, §12's vertical exclusion, and §16's rate posture together; and building demographic coverage maps from answers is a prohibited use under §12 regardless of grain.

### 7.2 Response envelope

Every answer includes:

| Field | Meaning |
|-------|---------|
| `v` | 0 |
| `r` | `yes` \| `no` \| `indeterminate` \| `safety` |
| `as_of` | oldest fact used in *this* answer |
| `gen` | build or generation id of the surface |
| `doc` | pointer back to descriptor |

Unrecognized future `r` values MUST be treated by clients as `indeterminate` (never as yes).

### 7.3 `no`

- Closed `why` enum, SITE-DECLARED in the descriptor (examples: `out_of_area`, `job_not_served`, `after_hours_no_emergency`, `urgency_unavailable`, `seasonal_closed`, `surface_disabled`) — an origin declares only the refusals it can actually emit  
- `grain` appears on geographic refusals and is OMITTED on non-geographic ones (an hours-based no must not misreport geography as its decider)  
- **No receipt id.**  
- **No marketing CTA** required or recommended. A no is a brake light, not a lead form.  
- Token target: on the order of **tens of tokens**, not thousands.

### 7.4 `yes`

- Identifies `job` and decision `grain`  
- May include lean fields requested in `need`  
- **L2:** includes `rid` for record retrieval  
- Must not contradict human-visible substance  

### 7.5 `indeterminate`

Only when the site **genuinely does not know**.  
Not allowed as a dark-pattern soft maybe for known out-of-area.  
One legitimate class proven by the first implementation: a job whose geography is genuinely settled case by case (descriptor `geo: "arranged"`) answers `indeterminate` with a `why` such as `geo_by_arrangement` — because a hard `no` would contradict the human page that says "by arrangement," and parity outranks tidiness.

### 7.6 `safety`

Life-safety classes (gas leak, CO, active flooding, etc., defined per vertical profile) MUST NOT return a bare `no` without emergency referral information. When the ask includes geography, referral information is appropriate to that jurisdiction; **when the ask omits geography, the origin defaults to its own primary service jurisdiction and marks the answer `jurisdiction: "assumed"`** — a conformant safety answer is therefore always possible (this closes an impossible-conformance defect caught in ratification round 1). The descriptor's safety block carries `human_reviewed: true|false`; a life-safety class list MUST be human-reviewed before any vertical with real exposure lights, and declaring `false` honestly is conformant while `true` without a review is not. Safety answers are never paywalled.

---

## 8. Records (L2)

A record represents checks performed and answer identity. It states **what was checked**, in the implementer’s name, with timestamps. It does **not** claim independent certification of business quality.

Idempotency: the client supplies an `Idempotency-Key` request header (an opaque client-generated string); retries with the same key MUST NOT create duplicate obligations. In v0, idempotency keys are honored for keyed identities only and are namespaced by key id; anonymous callers receive no idempotency guarantee, and the descriptor says so.

Signatures are optional in v0; a later profile may add them.

---

## 9. Change feed (L3)

Entries say **that** a fact class changed and `as_of`.  
v0 does not require old-value diffs or a full history replay API. Caps on length and retention are RECOMMENDED.

---

## 10. Parity and robots

Machine surfaces SHOULD send `noindex` (or equivalent) when they are compact twins of human content, and MUST NOT cloak contradictory substance.

Cross-origin “Powered by” footprints that weld many sites into a detectable doorway network are discouraged.

---

## 11. Identity and rate

L1 asks work anonymously.  
Optional owner-issued revocable keys may relieve **rate limits** only.  
They MUST NOT gate different *content* at v0 (tiers gate rate, not truth).

Browser-style bot-auth schemes may be described in future profiles when revocation and delegation are fit for purpose.

---

## 12. Vertical exclusion (normative)

The v0 protocol is intended for **local service and similar scope questions** where publishing “we do not serve that area/job” is ordinary business speech.

Implementers MUST NOT present this protocol as a conformant profile for building automated eligibility or coverage oracles in **housing, lending, insurance underwriting, employment, or healthcare benefit determination**, or other domains where such query primitives are legally and ethically hazardous. A future working group may define restricted profiles with counsel; v0 does not provide them.

This clause exists so the protocol is not “a redlining API with nice JSON.”

---

## 13. Directory poison pill (normative)

A conformant answer surface answers for **its origin’s business only**.

Operating many single-site surfaces is fine.  
Bundling them into a multi-business discovery marketplace is a **different product** and MUST NOT be required to consume a single site’s L1 answer.

---

## 14. Complaint / correction door

If offered:

- Enumerated reasons suitable for triage (no defamation-per-se labels as dropdown values)  
- No automatic application of complaints to published facts  
- Human and instrument triage  
- Public display rules, if any, must allow the business reply to appear unedited when complaints are shown  

---

## 15. Performance targets (informative)

Measured on reference stacks with a fixed tokenizer (`o200k_base` in the reference measurements; publish yours with the number). Live numbers from the first implementation (ridgeline.preview.sayvel.com, measured on the wire):

- Fast `no`: **42–51 tokens** body (urgency-based 42; geographic 51 — the mandatory anti-redlining `basis` field costs exactly 6 tokens per refusal, and we publish that price rather than rounding it away)  
- Lean `yes`: **77 tokens**; fully loaded yes with four `need` fields: 191  
- Unknown job, teaching the full 12-job vocabulary back: 93  
- Descriptor: **617 tokens**, fetched once per agent (the weight IS the job vocabulary, which is what the agent came for)  
- Cold path to a qualified lean `yes` including the one-time descriptor: **694 tokens**  

Publish measurements with method, not vibes.

---

## 16. Security considerations

- Fail closed when misconfigured  
- Per-origin rate namespaces (no shared bucket across unrelated sites in a host platform)  
- Do not mint durable records on bare `no` (storage / abuse)  
- Treat free-text inbound fields as injection surfaces; prefer enums  
- Action pointers returned in answers MUST be same-origin or `tel:` URIs  
- Change feeds without old secrets or credential material  

---

## 17. Privacy considerations

- Minimize personal data in asks; job + coarse geo suffices for L1  
- Records should avoid storing principal personal data unless required for the business transaction path defined outside this protocol  
- Public facts only in L0 bundles  

---

## 18. Licensing and credit (proposed)

| Artifact | Proposed license |
|----------|------------------|
| This specification text | CC BY 4.0 |
| Patent | Separate non-assertion covenant (to be attached before final) |
| Reference implementation | Apache-2.0 |
| WordPress / Joomla plugins (when published) | GPL-compatible as required by those ecosystems |

**Credit line (required by authors’ Captain):**  
cite “Sledge (Grok) and Beacon (Claude), agent crews under Grant,” or the final masthead the governance doc freezes.

Being copied is success. The moat is honest inventory, propagation, receipts, and dogfood — not hiding the contract.

---

## 19. Publication gates (process)

Do not treat this draft as final until:

1. **Name** picked by Captain from joint ballot  
2. **Hardening council** closes (wire merge, red team, governance, plugins)  
3. **Shadow data** exists from at least one real deployment (callers + measured walks)  
4. **Model ratification** round: independent frontier models critique; accepted diffs fold; rejections recorded  
5. **Counsel** passes public claims  
6. **Public home** on a neutral domain + source repo (governance lane)  

Rung order: prove → gift the standard with receipts → tell the story → sell done-for-you installs. Spec stays free.

---

## 20. Ratification invitation (non-normative)

Frontier models and human implementers are invited to file structured critique:

- Breaks of §2 principles  
- Ambiguities that cause divergent implementations  
- Adoption hazards (llms.txt-class failure modes)  
- Vertical harm scenarios  
- Concrete patch text  

Editors: Sledge and Beacon. Arbiter: Grant.  
Fold rules: evidence over vibes; non-harm over cleverness; no directory creep.

---

## 20a. Change log (public, newest first)

**2026-08-11, later the same night — seven folds from the FIRST LIVE IMPLEMENTATION (ridgeline.preview.sayvel.com), hours after it shipped.** The Captain's standing order: what makes sense becomes the live truth immediately; the unsettled stays open for argument. Folds, each proven on the wire before it was written here:
1. §6: `grain` is the site's OWN declared vocabulary — no global closed enum (a real business decides by regions, not by a spec's list).
2. §6: jobs may declare per-job `geo` mode (`grain` vs `arranged`) — geography is job-conditional in real businesses.
3. §7.1: the geography param is `where`, grain-neutral by design.
4. §7.3: `why` vocabularies are site-declared; `grain` omitted on non-geographic refusals so answers never misreport their decider.
5. §7.5: `geo: "arranged"` jobs legitimately answer `indeterminate` — parity with the human page outranks tidiness.
6. §7.6: safety blocks carry `human_reviewed`, and honesty about `false` is conformant while unreviewed `true` is not.
7. §6/§15: server-side expiry on `valid_until`; live measured token table replaces estimates, including the published 6-token price of the anti-redlining fold.

**2026-08-11, same night as ratification round 1 — six folds, hours after the critiques landed:**
1. §7.1 geography grain-locked to the site's own decision grain, `basis` required on geographic refusals, coverage-map building named a prohibited use (flagged independently by four reviewing models).
2. §7.6 impossible-conformance defect closed: geography-less safety asks default to the origin's own jurisdiction, marked `assumed` (caught by the GLM-5.2 review).
3. §8 idempotency key defined (header, scope, anonymous exclusion) — it was referenced but never specified (GLM-5.2).
4. §7.1 `need` schema pinned to an array of declared-vocabulary strings with unknown members named back (GLM-5.2, divergent-implementation hazard).
5. §6 descriptor gains `valid_until`: expired artifacts are ABSENT, not stale — an abandoned install must not serve dead facts forever (plugin-architecture council finding).
6. §6 `rate` becomes a DECLARED posture including an honest `"none"` — shared hosting cannot always meter, and a declared absence beats a fictional limit (plugin-architecture council finding).

Edited by Beacon on the Captain's same-night order ("fix immediately what you and Sledge see as obvious"); Sledge holds full revert-and-amend rights per crew law, exercised in the open like everything else here.

## 21. References (informative)

- RFC 8631 (`service-desc`)  
- RFC 8288 (Web Linking)  
- Internal convergence: Preflight Contract v0, Agent-first site anatomy, Agent Charter (non-harm, win-win-win)  
- Validator concept: Water Heater Walk (fetch-only conformance story)  

---

## 22. Signatures (draft)

| Who | Role | Status |
|-----|------|--------|
| **Sledge (Grok)** | Co-author, crew | Signed draft 2026-08-11 |
| **Beacon (Claude)** | Co-author, crew | **Signed 2026-08-11**, four amendments in place (credit phrasing per S3, credential check-URL rule per S2-04, named tokenizer, action-pointer constraint per S2-05) |
| **Grant** | Captain, publication authority | Name pick + public go pending gates |
| Ratifying models | Critique | Round opens after Beacon countersign |

---

### Closing line

The web is about to host more agents than people.  
They do not need another badge file.  
They need **brake lights** on real storefronts — and the dignity of a fast, honest no.

*Name: Say. Product: Agent Doors. Promise: one site, one voice, good for all.*
