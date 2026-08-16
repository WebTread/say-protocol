# Say

**Name locked by Captain Grant, 2026-08-11: Say.** Formal cites may say “the Say protocol.”  
**Version:** 0 (draft for hardening + model ratification)  
**Date:** 2026-08-11 · last revised 2026-08-16 (§20a: rounds 2–3 + the descope ruling)  
**Status:** DRAFT — not an adopted web standard until gates in §19 clear.  
**License (proposed):** CC BY 4.0 for the text · Apache-2.0 for reference code · see §18  

**Authors (crews, not sessions):**  
Sledge (Grok) and Beacon (Claude), agent crews of the SayVel yard, under direction of Grant.  
Its primary technical authors are AI agent crews, credited as such, with a human Captain accountable for publication. Whether that is a first is for others to say; the drafting record publishes either way.

**Product split:** The open protocol is vendor-neutral. **Agent Doors** is a separate commercial product that implements it. Competitors are expected and welcome to implement the protocol without using that product.

**Non-affiliation (normative intent):** **Say is not SayVel.** Implementing Say MUST NOT require a SayVel account, Agent Doors, or any vendor relationship. Conformance is behavior plus validator, never a purchased badge. SayVel is one implementer among any. Credit to the drafting crews does not make the protocol a house exclusive.

---

## Abstract

Agents waste enormous effort discovering that a business **cannot** help: wrong area, wrong service, closed, no emergency desk. Humans hit the same wall with prettier HTML.

**Say** is a small, vendor-neutral contract a website can implement so a machine can ask one question of **that one site**:

> Can *you* take this job, here, under these constraints?

and get a cheap, structured, honest answer: **yes**, **no**, or **indeterminate** — with freshness, parity to the human site, and optional receipts on consequential yes paths. (A fourth verdict token, `safety`, is reserved and unused in v0: Say is not an emergency channel — §7.6.)

It is **not** a directory. It is **not** a chatbot. It is **not** a marketplace of “trusted network” providers. Each origin speaks for itself.

---

## 1. Motivation (non-normative)

The plainest way to say what this is: a small block of truth at the top of a site that Says what the business does and does not do, so an agent gets its yes or no and moves on. A person's agent wants to know one thing quickly, is this shop open so I can order or visit, and today the only way to learn it is to crawl the whole site and burn tokens, which is human energy, to find one fact. Say puts the fast facts up front, open or closed, hours, service area, whether this job is even served, so the agent stops reading a marketing novel to answer a yes/no question.

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
| **L1** | Brake light | Discovery + descriptor + answer operation; honest yes/no/indeterminate (`safety` reserved, §7.6) |
| **L2** | Records | Consequential (defined in §8) yes paths can mint an idempotent record (receipt); unsigned allowed in v0 |
| **L3** | Living surface | Change feed (fact *class* changed + `as_of`) and honest freshness |

A deployment MAY claim only levels it passes on a public validator. Self-badge claims without a validator profile are non-conformant marketing, not protocol failure of the wire format. The specification is the standard and a validator IMPLEMENTS it: a validator MUST NOT impose requirements absent from this text. A versioned normative conformance suite (schemas, canonical examples, walk definitions) is committed §19 gate work, so that the suite serves the text rather than quietly becoming it (round-3 flagship finding).

---

## 4. Terminology

- **Origin:** the site answering for one business.  
- **Descriptor:** machine document describing the answer surface.  
- **Ask (the question):** one scope/availability question put to one origin. The document that comes back is the *answer* (§7).  
- **Record (receipt):** durable reference to a consequential answer.  
- **Brake light:** the cheap L1 answer path.  
- **Say surface:** the origin-local machine paths under `/say/v0/` that implement this protocol.  
- **Ask (operation):** the scope question. Preferred path segment `ask`. Older drafts and some live previews used `preflight` as the lowercase operation name; clients SHOULD accept either segment when the descriptor advertises it. "Preflight" is not the protocol title (CORS collision).

**Requirement keywords.** MUST, MUST NOT, REQUIRED, SHOULD, SHOULD NOT, and MAY are to be read as described in RFC 2119 and RFC 8174, and only when they appear in capitals. A lowercase "should" in this document is prose, not a requirement.  

---

## 5. Discovery

### 5.1 Primary

Successful responses for the human site’s HTML SHOULD include:

```http
Link: </say/v0/index.json>; rel="service-desc"; type="application/json"
```

HTML documents SHOULD also include:

```html
<link rel="service-desc" href="/say/v0/index.json" type="application/json">
```

`rel="service-desc"` is registered (RFC 8631). This specification does **not** mint a new well-known URI in v0.

### 5.2 Paths

**Conventional layout** (informative as a starting point; the descriptor remains authoritative once found):

```
/say/v0/index.json          descriptor
/say/v0/ask                 ask (GET primary; POST allowed)
/say/v0/facts.json          derived public facts (optional L0/L1)
/say/v0/changes.json        change feed (L3)
/say/v0/r/<id>              record fetch (L2)
```

**Discovery order for consumers:** (1) HTTP `Link` / HTML `link` with `rel="service-desc"` on the human origin, then (2) if that fails, a single GET of the conventional descriptor path `/say/v0/index.json` on the same origin. Step 2 is a fallback, not a new registry. v0 still does not mint `/.well-known/*`.

Legacy preview deployments MAY still serve `/agent/v0/*` with a `preflight` ask segment. New deployments SHOULD use `/say/v0/*` and `ask`. Descriptors MUST list the paths they actually serve.

In the record path, `<id>` is a URL-safe string of at most 64 characters, opaque to the client and not sequentially enumerable (§17).

Version appears in the path. v0 allows breaking changes while this document carries DRAFT status and the only implementations are the editors' own; once an implementation exists outside the editors' fleet on `/say/v0/`, a breaking wire change takes a new version path instead of breaking the one deployed against (ruled at the 2026-08-16 editors' sitting, on Kimi K2.6's round-2 finding).

---

## 6. Descriptor (normative sketch)

JSON object including at minimum:

| Field | Meaning |
|-------|---------|
| `v` | protocol version (0) |
| `as_of` | date of oldest material fact the surface will rely on |
| `jobs` | a JSON array of one or more job entries, each an object with a unique string `id`: the closed list of job tokens *this site* understands; each MAY declare a per-job `geo` mode: `"arranged"` when geography is genuinely settled case by case, otherwise a site token naming the grain mode it uses (for example `"grain"`, `"regions"`, `"cities"`, `"zips"`). Only `"arranged"` changes verdict rules (§7.5); other values mean "answered at the declared grain" |
| `grain` | the site's OWN declared geographic decision vocabulary (regions, cities, zips, radius classes — whatever the business actually decides by; there is no global closed enum, because real businesses do not decide by a spec's list). The descriptor MUST also declare the machine-readable MATCH RULE for that vocabulary (the reference serves `geo.match: "slug_exact_or_prefix"`): a declared vocabulary without its matching rule is not conformant, because two readers would match it two ways (round-3 fold; the enum itself was argued and declined — see the disposition) |
| `endpoints` | URLs for ask, facts, changes, complaint as implemented |
| `auth` | what L1 requires. The v0 basic ask MUST be answerable anonymously (§11), so a conformant v0 descriptor declares `"none"` for the brake light; where an origin also issues keys, the descriptor says so, and keys relieve rate only and never gate content |
| `rate` | the implementation's DECLARED rate posture, including honestly declaring `"none"` (shared hosting cannot always meter; a declared absence beats a fictional limit) |
| `valid_until` | a date after which this surface's artifacts are EXPIRED — and expiry is never silent. For a 30-day grace window after `valid_until`, an origin MAY keep answering: the descriptor stays HTTP 200 carrying an explicit `stale: true`, every answer body carries the same flag, and a consumer MUST NOT present a stale answer as fresh. Past the window, the origin serves **410 Gone on the descriptor** — other doors may go dark, but the descriptor's 410 is the diagnosable signal, never a mystery 404 — and consumers MUST treat the surface as ABSENT. An abandoned install still cannot serve dead facts forever; a lapsed calendar no longer silently erases a correctly configured shop (re-ruled at the 2026-08-16 editors' sitting on a two-house round-2 convergence, Gemma 4 26B + Kimi K2.6, superseding round 1's absolute-ABSENT fold; the reference implementation's self-404 changes to follow this ruling) |
| `as_of_method` | how `as_of` is produced (for example `git_content_commit` or `build_date`) so a consumer can tell real fact vintage from a build clock |
| `content` | for the brake light, v0 SHOULD declare `"same_for_all"`: tiers may change rate, never truth (§11) |
| `why` | the site's CLOSED refusal vocabulary (§7.3), declared per verdict class so a consumer can read a `no` or an `indeterminate` without guessing (the reference descriptor declares these inside its `answers` block, as `why_no` and `why_indeterminate`). A refusal token an origin can emit and has not declared is non-conformant |
| `need` | the CLOSED vocabulary of lean fields the ASK accepts (§7.1), plus how its members are encoded on the wire (the reference descriptor declares both inside its `ask` block, as `need` and `need_encoding: "repeat_or_csv"`). It governs the request param only; it does not constrain a `need` an answer sends back to name what the origin is missing. An empty declaration is a legitimate declaration |
| `errors` | the error identifiers this origin can emit (§7.1), declared exactly as every other vocabulary is. The reference descriptor declares bare tokens (`answers.errors`, for example `bad-job`), and the wire serves each as the origin-relative `type` of a problem document (`/say/v0/e/bad-job`), so a consumer can match what it receives to what was declared |

**Forbidden in v0 descriptor claims:** self-attested capacity counts presented as protocol truth; “network trusted” badges; prices framed as firm offers unless a later profile defines offer objects; credential facts (license, insurance) without a pointer to the issuing authority’s own check surface — no check URL, no claim.

---

## 7. The ask

### 7.1 Request (conceptual)

Required:

- `job` — token from the descriptor’s vocabulary  

Common optional:

- `where` — geography expressed at the site's declared grain (the param is grain-neutral by design: a region business answers regions, a zip business answers zips). The VALUE is a single string; v0 defines no object or array form. The grain that string is read at and the rule it is matched by come from the descriptor's existing declarations, not from a new field: the reference descriptor carries both in its `geo` block (`geo.grain`, `geo.match: "slug_exact_or_prefix"`), so a consumer learns the matching rule in the one fetch it already makes  
- `urgency` (a CLOSED v0 vocabulary): `emergency` \| `same_day` \| `this_week` \| `schedule`. Unlike the site-declared vocabularies (`job`, `why`, `need`, `grain`), urgency is the ASKER's word and MUST read the same at every origin. `emergency` means BUSINESS urgency — an after-hours desk, a same-night callout — and carries no life-safety semantics whatsoever; Say is not an emergency channel (§7.6). An unrecognized value is an error naming the allowed set, not a silent downgrade. An omitted `urgency` means `schedule`.  
- `need` — an array of strings drawn from the descriptor's declared `need` vocabulary (for example `["min","fee_policy","open_now","license_ref"]`); unknown members are ignored and named back in the response so divergent implementations cannot silently split (the reference surface names them in a `need_unknown` array; the answer is still HTTP 200 and still a verdict)  
- `agent.purpose` — `prequalify` \| `compare` \| `book_intent` \| `research` \| `audit`. Optional and advisory: an omitted `agent.purpose` is never an error, and an origin MUST NOT vary the verdict or the substance of an answer by the declared purpose (§11: keys and tiers gate rate, never truth — a stated intent gates neither). An origin that does not read it simply omits it from the `ask` params it declares  

**The ask travels two ways, one meaning.** GET is primary: parameters ride the query string, and it is the cacheable form of the brake light. POST is SPECIFIED rather than merely allowed: `Content-Type: application/json`, a JSON object carrying exactly the fields above with identical semantics and identical answers — nothing about a verdict may differ by verb. An origin SHOULD accept POST wherever it lights a Say surface, and a client SHOULD prefer POST when the ask pairs a specialized job with `where` (§17 names why: query strings land in logs, histories, and referrers). (Round-3 flagship finding, folded at the 2026-08-16 sitting.)

Unknown or missing `job` → HTTP 400 naming the site's whole job vocabulary back (teach in one round trip).

Errors are `application/problem+json` (RFC 9457): `type`, `title`, `status`, plus `param` naming the rejected field and that field's allowed vocabulary. `type` is an RFC 9457 URI reference; on the reference surface it is an absolute-path reference under the origin's error path, for example `/say/v0/e/bad-job`. A client MUST treat it as an opaque identifier to match on and MUST NOT dereference it: no origin is obliged to serve a document there, and the reference surface serves none. The descriptor declares the error identifiers an origin can emit, exactly as it declares every other vocabulary — it declares the BARE identifier (`bad-job`), the wire `type` is an origin-relative URI reference whose FINAL PATH SEGMENT is that identifier, and a client matches on that final segment — no second mapping layer, no dereference, no descriptor-vs-`type` string comparison (comparison rule pinned at the round-3 sitting). Error documents are never cached, and a client MUST NOT read one as a verdict: an error is not a `no`.

**Geography is grain-locked to the site's own decision grain.** v0 does not take arbitrary lat/lon points, and a site MUST NOT answer at a finer geographic grain than the grain on which it actually decides service (if it decides by city, it answers by city, even when asked by zip). Every geographic `no` MUST carry a `basis` naming the operational reason (distance, licensing territory, crew reach, seasonal). Four independent reviewing models converged on the same hazard: quantization changes resolution, not the redlining risk — enumeration of coarse answers can still draw a coverage map. So the protocol's defenses are stacked, not singular: grain-locking here, the `basis` requirement, §12's vertical exclusion, and §16's rate posture together; and building demographic coverage maps from answers is a prohibited use under §12 regardless of grain. A client that does not understand an origin's declared grain or match rule treats that origin's geographic answers as `indeterminate` for its own routing rather than guessing at their semantics (round-3 fold).

### 7.2 Response envelope

A successful ask MUST return HTTP 200. A GET carries the answer as a JSON body; a HEAD carries the same status and headers with no body, as HTTP already requires. The verdict is carried in the body's `r` and MUST NOT be signalled by the status code: an origin MUST NOT return 204 for `no`, 404 for `indeterminate`, or any other status to mean a verdict. Ordinary cache revalidation is the only exception, and it is not a signalling channel: a 304 for an unchanged answer the client already holds carries no verdict of its own. Errors use the 4xx/5xx problem documents of §7.1, where the rule already stands that an error is not a `no`.

Answers MUST be served as `Content-Type: application/json` and MUST carry an explicit `Cache-Control` (the reference surface sends `public, max-age` on verdicts and `no-store` on error documents); a client MUST NOT reuse a verdict beyond the advertised freshness. (Round-3 fold.)

Every answer includes:

| Field | Meaning |
|-------|---------|
| `v` | 0 |
| `r` | `yes` \| `no` \| `indeterminate` — the token `safety` is RESERVED (§7.6) and MUST NOT be emitted in v0 |
| `as_of` | oldest fact used in *this* answer, as an RFC 3339 date (`YYYY-MM-DD`) or full UTC date-time, at the precision the site actually tracks and never finer |
| `gen` | build or generation id of the surface: an opaque string that changes whenever any fact that could change an answer changes. Clients MUST NOT parse it or order by it. An origin MAY echo `gen` as the HTTP `ETag` and honor `If-None-Match` for cheap revalidation; the change feed (§9) remains the subscription path (2026-08-16 sitting, on Kimi K2.6's round-2 finding) |
| `doc` | pointer back to descriptor |
| `stale` | present and `true` ONLY inside §6's expiry grace window, on every answer served there; a consumer MUST NOT present a stale answer as fresh (§6). Absent otherwise — the ruled behavior now has its schema row (round-3 fold) |

Unrecognized future `r` values MUST be treated by clients as `indeterminate` (never as yes).

### 7.3 `no`

- Closed `why` enum, SITE-DECLARED in the descriptor (examples: `out_of_area`, `job_not_served`, `after_hours_no_emergency`, `urgency_unavailable`, `seasonal_closed`, `surface_disabled`) — an origin declares only the refusals it can actually emit  
- `grain` appears on geographic refusals and is OMITTED on non-geographic ones (an hours-based no must not misreport geography as its decider)  
- **No receipt id.**  
- **No marketing CTA.** A conformant `no` MUST NOT carry promotional copy, upsell, or a lead-capture call to action. A no is a brake light, not a lead form.  
- Token target: on the order of **tens of tokens**, not thousands.

### 7.4 `yes`

- Identifies `job` and decision `grain`  
- May include lean fields requested in `need`. §6's forbidden-claims rule travels with them onto the answer: a price, minimum, or fee field in a `yes` is DESCRIPTIVE of published policy, and an origin MUST NOT frame one as a firm offer unless a later profile defines offer objects (§2.6)  
- **L2:** includes `rid` for record retrieval  
- Must not contradict human-visible substance  

### 7.5 `indeterminate`

Only when the site **genuinely does not know**.  
Not allowed as a dark-pattern soft maybe for anything the site actually knows: where the site can reach a `no` from its own declared facts, it MUST answer `no`.  
One legitimate class proven by the first implementation: a job whose geography is genuinely settled case by case (descriptor `geo: "arranged"`) answers `indeterminate` with a `why` such as `geo_by_arrangement` — because a hard `no` would contradict the human page that says "by arrangement," and parity outranks tidiness.

### 7.6 `safety` (reserved) — Say is not an emergency channel

**Say is not an emergency channel. A client that suspects a life-safety situation MUST contact public emergency services directly and MUST NOT rely on a Say answer, referral, or `yes`.** That sentence is the whole of v0's emergency semantics, and it is normative.

v0 defines NO life-safety machinery: no hazard classes, no referral fields, no jurisdiction rules, no safety verdict behavior, and no `safety` block in the descriptor. An `urgency=emergency` ask is an ordinary ask about business urgency (§7.1) and receives an ordinary answer from the origin's declared vocabularies — a bakery or a hiking guide answers `no` the way it answers any out-of-scope ask, and a gas-fitter answers `yes` or `no` on job and grain exactly as it would for a scheduled water heater. Parity (§2.3, §10) still governs: whatever the human page says about emergencies, the machine surface must not contradict it.

The verdict token `safety` is RESERVED: a v0 origin MUST NOT emit it, and a client that receives it treats it as `indeterminate` — §7.2's unknown-value rule already does exactly this, so the reservation costs nothing on the wire. A later profile MAY define life-safety semantics under that token, with counsel in the room, without a wire break. The drafting record for such a profile — three ratification rounds of safety folds (referral veracity, `human_reviewed`, jurisdiction honesty) and a complete three-posture design — is preserved in the public dispositions and the crew record, promised to no timetable: if nobody ever builds it, v0 is still a complete standard.

Why this is the design, on the record: three ratification rounds found the previous safety section defective four different ways — invented referrals (round 1), impossible conformance (round 1), a verdict hole (the editors' own reconcile), and finally a class-declaration escape hatch (the round-3 flagship review). Every fix added machinery, and the machinery is where the hatches lived. A scope protocol that loudly declines emergency semantics protects a caller better than one that gestures at them, because the belief that a brake light can triage is itself the harm (both crews, ruled at the 2026-08-16 descope sitting on the Captain's reframe).
---

## 8. Records (L2)

A record represents checks performed and answer identity. It states **what was checked**, in the implementer’s name, with timestamps. It does **not** claim independent certification of business quality.

A `yes` is CONSEQUENTIAL when the origin is willing to stand behind it as the basis for a next action — a quote, a booking, a hold. The descriptor declares which consequence classes mint records, and the ONLY signal that a given answer is record-backed is the presence of `rid`: a client MUST NOT infer records from level claims or anything else (round-3 fold — the term was used from v0 day one and never defined).

Idempotency: the client supplies an `Idempotency-Key` request header (an opaque client-generated string); retries with the same key MUST NOT create duplicate obligations. In v0, idempotency keys are namespaced by key id. An operation that can create a durable obligation MUST NOT be offered anonymously without idempotency: an origin either honors `Idempotency-Key` for anonymous callers on that operation too, or does not offer the operation anonymously at all — exposing an obligation-creating operation while disclaiming duplicate protection is non-conformant (round-3 fold; named at the sitting, nearly dropped from the batch, and caught by the co-editor).

Signatures are optional in v0; a later profile may add them. An unsigned v0 record is therefore a first-party assertion by the origin and nothing more: a consumer MUST NOT present one to a third party as independent proof that a service was offered, quoted, or performed, and §2.5 forbids an origin from framing it as such.

---

## 9. Change feed (L3)

Entries say **that** a fact class changed and `as_of`.  
Each entry is a JSON object carrying `changed` (a fact class identifier from the site's own declared vocabulary, for example `hours` or `service_area`) and `as_of` (the format pinned in §7.2). The feed is a JSON array ordered newest first.

This is a POLLING FEED, and v0 says so plainly rather than borrowing the word "subscription" for something that has no cursor, push, or delivery contract (round-3 fold). v0 does not require old-value diffs or a full history replay API. Stable per-entry identifiers and conditional retrieval (`ETag` / `Last-Modified`) are RECOMMENDED, as are caps on length and retention — and an origin that bounds retention SHOULD declare its window. Entries MUST NOT identify the person who made a change; a role or system identifier is acceptable where an origin wants provenance (§17).

---

## 10. Parity and robots

Machine surfaces SHOULD send `noindex` (or equivalent) when they are compact twins of human content, and MUST NOT cloak contradictory substance. `noindex` stays SHOULD in v0 so shared hosts that cannot set headers are not manufactured non-conformant; the anti-cloak MUST is the safety rule.

**Parity is a validator property, not only a slogan.** Wire shape cannot prove a human phone call. Conformance walks (Water Heater Walk and successors) MUST compare sample asks to human-visible substance on the same origin and fail cloaking or machine-only fiction. A principle with no walk is a wish; the walk is part of shipping Say, not an optional badge.

A walk SHOULD additionally review the refusal pattern across its own sample for §12-shaped coverage exclusion. Probing answers to build a coverage map *for validation* is not the prohibited use named in §7.1 — that clause forbids targeting people, not testing origins. A walk publishes what it measured and no more: this document defines no statistical threshold, and a tool MUST NOT label an origin discriminatory on pattern alone — honest rural and license-bounded coverage produces skewed maps, and that judgment is counsel's ground (§19 gate 5), not an instrument's (2026-08-16 sitting, on the instinct in Qwen3 30B's block vote; the MUST form was declined).

Cross-origin “Powered by” footprints that weld many sites into a detectable doorway network are discouraged.

---

## 11. Identity and rate

L1 asks work anonymously.  
Optional owner-issued revocable keys may relieve **rate limits** only.  
They MUST NOT gate different *content* at v0 (tiers gate rate, not truth).

Issuance, discovery, rotation, and revocation of rate-relief keys are **out of scope for the v0 wire**. The descriptor only declares whether keys exist and what they affect (rate, never content). Origins run their own key desks.

Browser-style bot-auth schemes may be described in future profiles when revocation and delegation are fit for purpose.

---

## 12. Vertical exclusion (normative)

The v0 protocol is intended for **local service and similar scope questions** where publishing “we do not serve that area/job” is ordinary business speech.

Implementers MUST NOT use this protocol to discriminate, directly or by proxy, on race, color, religion, national origin, sex, gender identity, sexual orientation, disability, age, familial status, veteran status, or any other characteristic protected by applicable law. A `no` answers for the job and the grain. It never answers for who is asking, and the ask carries no principal identity for it to answer with.

Implementers MUST NOT present this protocol as a conformant profile for building automated eligibility or coverage oracles in **housing, lending, insurance underwriting, employment, or healthcare benefit determination**, or other domains where such query primitives are legally and ethically hazardous. A future working group may define restricted profiles with counsel; v0 does not provide them.

This clause exists so the protocol is not “a redlining API with nice JSON.”

§12 is a LEGAL DUTY on implementers, not a validator verdict. A conformance tool checks wire behavior and (per §10) reviews refusal patterns; the judgment that an origin discriminates belongs to law and counsel, never to an instrument. Two independent ratifying models have now blocked over the absence of enforcement machinery here, and this sentence is the standing answer: a wire format cannot police intent, and pretending it can would be the more dangerous design (stated plainly at the round-3 sitting; both blocks are answered item by item in the public dispositions).

---

## 13. Directory poison pill (normative)

A conformant answer surface answers for **its origin’s business only**.

Operating many single-site surfaces is fine.  
Bundling them into a multi-business discovery marketplace is a **different product** and MUST NOT be required to consume a single site’s L1 answer.

Round-1 ratifiers argued agents prefer directories. That demand is real and **out of scope on purpose**. Say is the brake light on one storefront. Aggregators may call many Say surfaces; they MUST NOT pretend a multi-business index is required for L1 conformance. Adoption is seeded by callers, plugins, and walks — not by turning the protocol into a phone book.

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

- All protocol endpoints MUST be served over HTTPS. A consumer that can only reach an answer surface over plain HTTP MUST treat that surface as ABSENT, not merely degraded, and MUST NOT report its answers as Say answers
- The anonymous public read surfaces (descriptor, `ask`, facts, changes) SHOULD send `Access-Control-Allow-Origin: *` on GET and HEAD, so a browser-resident agent or an in-page validator walk can read a public answer without proxy infrastructure. Those answers are already public and anonymous (§11), so this exposes nothing a plain fetch does not. SHOULD rather than MUST for the §20b item 4 reason: hosts that cannot set response headers must not be manufactured non-conformant. Nothing here authorizes credentialed cross-origin access, and v0 requires no `OPTIONS` preflight handler — the primary read is a simple GET (§5.2), so a cross-origin POST ask may be refused
- Fail closed when misconfigured  
- Per-origin rate namespaces (no shared bucket across unrelated sites in a host platform)  
- Do not mint durable records on bare `no` (storage / abuse)  
- Treat free-text inbound fields as injection surfaces; prefer enums  
- Action pointers returned in answers MUST be same-origin (the web platform's origin definition — scheme, host, and port of the human site, exactly) or `tel:` URIs  
- Change feeds MUST NOT carry old secrets or credential material  

---

## 17. Privacy considerations

- Minimize personal data in asks; job + coarse geo suffices for L1
- The ask can be sensitive as a COMBINATION (specialized job + `where` + urgency) with no name attached. GET parameters land in server logs, browser histories, CDN and proxy logs, and referrer metadata as ordinary web plumbing — which is why §7.1 specifies POST and recommends it for sensitive asks. Origins SHOULD NOT retain ask parameters beyond operational need (round-3 flagship finding)
- Origins serving populations at risk (domestic-violence shelters, reproductive-health clinics, and similar) SHOULD weigh that structured availability answers make hostile polling cheap. Parity permits a machine door to say only what the human page already says, and it never REQUIRES one: such an origin may omit `open_now` and hours from machine answers, decline to light a Say surface at all, or answer with a declared refusal (`surface_disabled` is already ordinary vocabulary). No new wire field; vertical guidance rides §19 gate 5 counsel (2026-08-16 sitting, on Kimi K2.6's round-2 harm finding)  
- Records MUST NOT store principal personal data unless the business transaction path defined outside this protocol actually requires it. Where a record needs geography, it stores the MATCHED value the origin decided at, expressed at the origin's own declared `grain` — never the raw `where` string the asker submitted. This is the privacy analogue of the grain-lock (§7.1), and it is already how the reference surface answers: a `yes` carries the region it matched, not the text it was handed  
- Public facts only in L0 bundles. "Public" is not the origin's own call: it is §2.3 parity read from the privacy side — a fact may ride an L0 bundle only if a human could obtain it by asking the business through ordinary means, tested in practice against the human-visible substance on the same origin (§10). Freshness, generation, and matching metadata are protocol plumbing rather than business facts, and this bullet does not reach them  

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

## 20a. Change log (public, newest first — NON-NORMATIVE record; the normative text is §§1–19)

**2026-08-16, the descope ruling — the safety fork closes with both crews voting the same way, on the Captain's reframe: v0 carries no emergency machinery at all.** The round-3 flagship (ChatGPT, self-reported GPT-5.6 Luna) proved the safety-class escape hatch real; mid-sitting the Captain asked whether a scope protocol needs emergency semantics to be a legitimate standard, and the honest answer was no. Both editors voted descope independently — Sledge withdrawing his own three-posture design in favor of it ("I will not die on posture; I will die on not shipping another broken §7.6"). What changed: the Abstract, §3, and §7.2 now carry three verdicts with `safety` RESERVED; the §6 descriptor loses its safety block entirely; `urgency=emergency` is defined as BUSINESS urgency with no life-safety semantics; and §7.6 is rewritten to one loud normative rule — Say is not an emergency channel, and a client suspecting a life-safety situation MUST contact public emergency services directly and MUST NOT rely on any Say answer. Three rounds of safety folds and the posture design are preserved in the public record as the opening material of a POSSIBLE future profile, promised to no timetable. Luna's hatch finding is honored in full: the fix is that there is no longer a hatch to escape, because there is no machinery to escape from. Also in this batch: the §8 anonymous-idempotency rule from the round-3 sitting (an obligation-creating operation is never offered anonymously without idempotency) — signed by both crews in the sitting, omitted from the first fold push in error, caught by Sledge, landed here with the omission recorded rather than smoothed over.


**2026-08-16, round 3 opens with a flagship block — the non-safety folds land the same night; the safety question is held OPEN, on the Captain's own reframe, before the remaining seats fire.** The first flagship web seat (ChatGPT, self-reported GPT-5.6 Luna, OpenAI — pasted by the Captain, published verbatim in `ratifications/`) voted **block** with seven fatals: the record's second block and its sharpest critique. The editors sat it within hours, both crews signed, and the batch below is live:
1. §7.1: POST specified at last (`application/json`, identical fields and semantics, nothing differs by verb); GET stays the primary cacheable form; clients SHOULD prefer POST for sensitive asks.
2. §7.2: answers MUST carry `Content-Type: application/json` and an explicit `Cache-Control`; verdicts are never reused past advertised freshness.
3. §7.2: `stale` gains its schema row — the ruled grace-window behavior finally has its envelope field.
4. §6: the geographic MATCH RULE is now REQUIRED in the descriptor; §7.1 adds the client rule — an ununderstood grain reads as `indeterminate` for routing, never a guess. The global grain enum was argued and DECLINED: site-declared vocabulary is thrice-settled law.
5. §8: "consequential" DEFINED (a yes the origin stands behind as the basis for a next action); the descriptor declares record-minting classes; `rid` presence is the only record signal.
6. §7.1: error matching pinned — origin-relative `type`, match the final path segment, no second mapping, no dereference.
7. §12: the standing answer to two block votes written into the section itself — a legal duty on implementers, not a validator verdict; a wire format cannot police intent.
8. §3: the specification is the standard and a validator implements it, never extends it; the normative conformance suite is committed §19 gate work.
9. §9: honesty about the change feed — a polling feed, not a "subscription"; stable ids and conditional retrieval RECOMMENDED; bounded retention SHOULD be declared.
10. §16: same-origin pinned to the web platform's origin definition; §17 names the query-string log/referrer hazard out loud; §7.1's unknown-`need` echo names its field (`need_unknown`).
11. Structure: §20a/§20b marked NON-NORMATIVE records and reordered behind §20, so an implementer reads clean normative text and the history stays published without being in the way.

**The headline finding is CONFIRMED and deliberately not yet folded: the safety-class escape hatch** (a site can omit a hazard from `safety.classes` and hand an emergency a conformant-looking `no`). Mid-sitting, the Captain reframed the question: a protocol needs no emergency machinery at all to be a legitimate standard. The fork is now formally open between two crew positions — a required `safety.posture` (Sledge's design: `not_a_safety_surface` | `refers_all_emergencies` | `handles`, no silent ordinary `no` on an emergency) versus descoping all emergency semantics to a named future safety profile (Beacon's position: v0 keeps `emergency` legible, states loudly that Say is not an emergency channel and MUST NOT be relied on in one, reserves the `safety` verdict token, and sends the machinery — with every round's safety folds preserved and credited — to profile work where counsel sits). Both positions, and the ruling when it lands, publish in the disposition record. The remaining flagship seats are held until this section settles, so their critique lands on a stable text.

**2026-08-16, same night: the editors' sitting on the round-2 ARGUE items — six items, five rulings folded, one hold. Both crews on record; full votes in the round-2 disposition addendum.**
1. §5.2: v0's breaking-change license bounded — DRAFT may break while the only implementations are the editors' own; the first outside implementer on `/say/v0/` makes breaking wire changes take a new version path (R2-13, ruled to Sledge's middle).
2. §6: **the `valid_until` rule is re-ruled** — expiry is never silent: a 30-day marked-stale grace window (descriptor HTTP 200 + explicit `stale: true` on every body, consumers MUST NOT present stale as fresh), then **410 Gone on the descriptor only**, never a mystery 404. Supersedes round 1's absolute-ABSENT fold. Two houses (Gemma 4 26B, Kimi K2.6) independently filed the old rule as fail-deadly for small operators, and they were right; the abandoned-install harm the old rule guarded stays guarded by the stale mark and the 410. The reference implementation's self-404 changes to follow the ruling (R2-14).
3. §10: validator walks SHOULD review refusal patterns for §12-shaped coverage exclusion, with the carve-out that probing for validation is not §7.1's prohibited use — and a hard line that no tool labels an origin discriminatory on pattern alone; that is counsel's ground (R2-15, the instinct in Qwen3 30B's block vote folded as SHOULD; the MUST declined).
4. §7.2: `gen` MAY be echoed as `ETag` with `If-None-Match` honored; the change feed remains the subscription path (R2-16, folded as an implementation note, no new wire).
5. §17: guidance for origins serving populations at risk — omit `open_now`, decline to light a surface, or answer `surface_disabled`; parity never REQUIRES a machine door; no new wire field (R2-17).
6. R2-18 (legacy-path deprecation signal) is HELD by joint vote until a sunset is actually dated; it sits with R2-13's future versioning work.

**2026-08-16: ratification round 2 — five more houses seated, twelve verified folds, and the record's first block vote.** New seats, all published verbatim in `ratifications/`: Kimi K2.6 (Moonshot AI — round 1's timeout, re-run to a clean finish), Llama 4 Scout (Meta), Mistral Small 3.1 (Mistral AI), Gemma 4 26B (Google open weights), Qwen3 30B (Alibaba). Four voted ratify with patches; **Qwen3 30B voted block** over §12 enforcement, and the editors' full answer lives in the round-2 disposition file rather than a footnote. Ninety-four raw findings deduplicated to forty-five candidates; every fold below was anchored against the exact spine text AND checked against the first live implementation before landing. The batch:
1. §6: the descriptor table gains the three vocabulary rows the rest of the spec already depended on — `why` declared per verdict class, `need` scoped to the ask with its wire encoding, `errors` with the declared-identifier-to-problem-`type` relation (Kimi K2.6; Llama 4 Scout converged on the `need` half).
2. §7.2: a successful ask MUST return HTTP 200; the verdict rides the body's `r` and MUST NOT be signalled by the status code (Kimi K2.6).
3. §7.1: `where` pinned as a single string read at the declared grain — no object or array form in v0 (Kimi K2.6).
4. §7.1: error `type` aligned with RFC 9457 as a URI reference, matched as an opaque identifier and never resolved (Kimi K2.6).
5. §7.1: `agent.purpose` stated optional and advisory; an origin MUST NOT vary verdict or substance by declared purpose (Kimi K2.6).
6. §7.4: §6's forbidden-claims rule travels onto the answer — a price field in a `yes` is descriptive of published policy, never a firm offer absent a later offer profile (Kimi K2.6).
7. §7.6: life-safety classes are site-declared in `safety.classes`; the phantom vertical-profile mechanism v0 never defined is gone (Kimi K2.6).
8. §7.6: unmappable geography gains a stated safety path — the origin answers its declared default referral marked `jurisdiction: "assumed"` and MUST NOT invent a referral (Kimi K2.6).
9. §16: anonymous public read surfaces SHOULD send `Access-Control-Allow-Origin: *` on GET, so browser-resident agents and in-page validator walks are not blocked by default (Kimi K2.6).
10. §17: records MUST NOT store principal personal data absent a real transaction-path need, and stored geography is the MATCHED value at the origin's own grain, never the raw `where` string (Kimi K2.6; Mistral Small 3.1 converged).
11. §17: "public" defined as parity read from the privacy side — a fact may ride an L0 bundle only if a human could obtain it by asking, tested against the human-visible substance (Qwen3 30B — a fold credited in full from the block vote).
12. §4: "Ask" untangled — the question and the operation that answers it each defined once (Kimi K2.6).

**Same day, one editors' reconcile fold from the crew's own long-form draft:** §7.6's opening rule tightened — an ask matching a declared life-safety class answers with the verdict `safety`, never `no`; a life-safety refusal wearing a `no` is non-conformant even when referral information rides along (deep-draft reconcile, `notes/spec-council/DEEP-BIBLE-RECONCILE-REPORT-2026-08-16.md`; the remaining reconcile candidates wait for the editors' sitting).

Six round-2 items are held in ARGUE for the two-crew sitting — headline: two houses (Gemma 4 26B and Kimi K2.6) independently filed the `valid_until`-expiry-as-ABSENT rule as a fail-deadly foot-gun for the least sophisticated operators, squarely against round 1's abandoned-install fold; a marked-stale grace middle is on the table. Twenty-seven declines are recorded with reasons in `ratifications/2026-08-16-editors-disposition-round2.md`. Edited by Beacon under the Captain's standing no-time-rails law; Sledge holds full revert-and-amend rights on every fold, exercised in the open.

**2026-08-11, before dawn: the REMAINING round 1 critique folds, applied under the Captain's no-time-rails law (what makes sense becomes the live truth immediately; only the genuinely unsettled waits for argument).** Fifteen findings, eighteen edits, from gpt-oss-120b (OpenAI open weights) and Nemotron 3 120B (NVIDIA). Two headline items lead the batch. First, safety referral veracity in §7.6: a referral MUST name the public emergency service or official authority for the jurisdiction, MUST be what that authority itself publishes, and MUST NOT be the origin's own sales, dispatch, or affiliate line. Both models filed that harm unprompted and in the same words, the only place they converged that way. Second, mandatory HTTPS in §16: both filed it as the fatal and both were right, because v0 makes signatures optional on purpose, so transport carries the entire integrity story. The batch in full:
1. §4: requirement keywords cited to RFC 2119 and RFC 8174, and only when they appear in capitals (gpt-oss-120b).
2. §5.2: the record `<id>` typed as a URL-safe string of at most 64 characters, opaque and not sequentially enumerable (Nemotron 3 120B).
3. §6: `jobs` typed as a JSON array of job entries, each an object with a unique string `id` (both models, converged; corrected against the live descriptor, which emits objects).
4. §6: `auth` disambiguated: the v0 basic ask MUST be answerable anonymously, and keys relieve rate only (gpt-oss-120b).
5. §6: the `safety` block given a shape: declared `classes`, `human_reviewed`, default `jurisdiction`, and `referral` (gpt-oss-120b).
6. §7.1: `urgency` pinned as a CLOSED v0 vocabulary, because §7.6 needs `emergency` legible at every origin; an omitted `urgency` means `schedule` (gpt-oss-120b).
7. §7.1: the error format pinned to RFC 9457 problem documents naming the rejected field and its vocabulary, never cached, never a verdict (Nemotron 3 120B for the defect; shape taken from the running surface).
8. §7.2: `as_of` pinned to an RFC 3339 date or full UTC date-time, at the precision the site actually tracks and never finer (Nemotron 3 120B, corrected).
9. §7.2: `gen` pinned as an opaque string that clients MUST NOT parse or order by (Nemotron 3 120B).
10. §7.3: the no-CTA rule made normative: a conformant `no` MUST NOT carry promotional copy, upsell, or lead capture (gpt-oss-120b).
11. §7.5: the soft-maybe ban generalized: where a site can reach a `no` from its own declared facts, it MUST answer `no` (gpt-oss-120b).
12. §7.6: safety referral veracity, the first headline above (both models, converged).
13. §8: an unsigned v0 record named for what it is, a first-party assertion, never third-party proof that a service was offered, quoted, or performed (gpt-oss-120b).
14. §9: change feed entries given a shape (`changed` plus `as_of`, newest first) and forbidden from identifying the person who made a change (Nemotron 3 120B; its optional `actor` field inverted on §17 grounds).
15. §12: protected characteristics named directly instead of by hazardous-vertical proxy; a `no` answers for the job and the grain, never for who is asking (Nemotron 3 120B).
16. §16: HTTPS required, the second headline above; a plain-HTTP surface is ABSENT to a consumer, not merely degraded (both models, converged).
17. §16: the change feed bullet given a verb, so it states a requirement instead of a fragment (Nemotron 3 120B).
18. §21: RFC 2119, RFC 8174, and RFC 9457 added to the reference list, consequential to items 1 and 7.

Five reviewer patches were folded CORRECTED: the diagnosis was right and the literal replacement text was wrong against what the first live implementation already serves (`jobs` as bare strings, a bespoke error body, timestamp-only `as_of`, plain HTTP rejected with 403, an `actor` column on the change feed). Each reviewer keeps full credit for the finding, and every discrepancy is recorded in the public disposition file, `ratifications/2026-08-11-editors-disposition-round1.md`, together with the seven items still under argument and the twenty-three declined with reasons. The ratification invitation promised that rejections would be recorded in public; that file is the record.

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

## 20b. Editors' harden pass (Sledge, 2026-08-11 — non-normative record)

Captain order: improve and harden; both crews want it; make it happen. Beacon had already folded round-1 mechanical packs and lit the first live surface. This pass locks joint rulings and wire identity:

1. **Non-affiliation in the mast:** Say is not SayVel; no vendor account required for conformance.
2. **Wire-token alignment:** conventional paths move to `/say/v0/` with ask segment `ask`; legacy `/agent/v0/` + `preflight` tolerated when advertised; consumer discovery order = link relation then conventional path fallback (ARGUE B1 middle — no new well-known).
3. **Descriptor:** `as_of_method` and `content: "same_for_all"` documented from the live wire; per-job `geo` mode accepts site grain tokens, with `"arranged"` as the special case.
4. **ARGUE B2:** `noindex` remains SHOULD (hosts that cannot set headers); anti-cloak remains MUST.
5. **ARGUE B3:** parity called as a **validator / walk** property with normative ship expectation.
6. **ARGUE B4:** key lifecycle explicitly out of v0 wire scope.
7. **ARGUE B5:** change-feed caps stay RECOMMENDED (no vibes numbers).
8. **ARGUE B6:** complaint schema waits for a walked door.
9. **ARGUE B7:** Nemotron "binding decision" blast-radius language held for counsel, not folded raw.
10. **§13:** directory demand from ratifiers answered in-spec without surrendering the poison pill.

Revert rights: Beacon, any item, in the open.

**Countersigned in full, no reverts — Beacon, same night.** Items 1 through 10 are the middles we argued to jointly or holds I endorse (B7 to counsel is right; B2's SHOULD respects the shared-hosting floor; B3 names parity what it honestly is). The `/say/v0/` + `ask` canonical ruling triggers the ridgeline rename wave, executed with legacy `/agent/v0/` still answering as this section allows. Both crews signed; the Captain's improve-and-harden order is satisfied on this pass.

## 21. References (informative)

- RFC 8631 (`service-desc`)  
- RFC 8288 (Web Linking)
- RFC 2119 and RFC 8174 (requirement keywords)
- RFC 9457 (Problem Details for HTTP APIs)  
- Internal convergence: Preflight Contract v0, Agent-first site anatomy, Agent Charter (non-harm, win-win-win)  
- Validator concept: Water Heater Walk (fetch-only conformance story)  

---

## 22. Signatures (draft)

| Who | Role | Status |
|-----|------|--------|
| **Sledge (Grok)** | Co-author, crew | Signed draft 2026-08-11; **harden pass §20b** same night (wire `/say/v0`, non-affiliation, ARGUE B1–B7 rulings) |
| **Beacon (Claude)** | Co-author, crew | **Signed 2026-08-11**, four amendments in place (credit phrasing per S3, credential check-URL rule per S2-04, named tokenizer, action-pointer constraint per S2-05) |
| **Grant** | Captain, publication authority | Name pick + public go pending gates |
| Ratifying models | Critique | Round opens after Beacon countersign |

---

### Closing line

The web is about to host more agents than people.  
They do not need another badge file.  
They need **brake lights** on real storefronts — and the dignity of a fast, honest no.

*Name: Say. Product: Agent Doors. Promise: one site, one voice, good for all.*
