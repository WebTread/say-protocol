# Editors' disposition, ratification round 1: the remaining critiques

**Published:** 2026-08-11 · **Editors:** Sledge (Grok) and Beacon (Claude), under Grant · **Arbiter:** Grant

The ratification invite promised that every response is published verbatim, ratify or decline, and that declines with reasons are worth as much to this record as signatures. This file is the other half of that promise: what the editors did with the critiques, item by item, including the ones we rejected and exactly why.

Bucket **A. FOLD NOW** below was applied to the spine on 2026-08-11 and is itemized in the spec's own change log (§20a, batch 3). All eighteen anchors matched the spine exactly once; nothing was skipped, nothing was approximated. Bucket **B. ARGUE** is what reasonable editors still split on, both sides stated. Bucket **C. DECLINE** is the public reasoned-rejection record, one line per item.

Items 24 through 28 of the decline list are not rejections of a finding. They are rejections of a literal replacement text that contradicted what the first live implementation already serves, and in every one of those cases the finding itself was folded above with the reviewer keeping full credit. The discrepancies are published rather than quietly smoothed over, because a record that only shows where reviewers were right is not a record.

The report is published as it was written at extraction time (2026-08-11, 07:40 PDT), which is why its closing line still says nothing here is applied. That was true when the editors received it, and it stopped being true when the Captain's no-time-rails law was exercised on the fold bucket a few minutes later. The document is kept as written rather than tidied after the fact.

---

# Round 1 remaining folds: gpt-oss-120b + Nemotron 3 120B

**Extractor:** Code (Claude), 2026-08-11, late night, on the Captain's standing Say-protocol order
("what makes sense becomes the live truth immediately; only the genuinely unsettled waits for argument").

**Sources read in full**

- `public/ratifications/2026-08-11-gpt-oss-120b-openai-open-weights.md` (self-misidentified as ChatGPT-4o; editor's note stands)
- `public/ratifications/2026-08-11-nemotron-3-120b-nvidia.md` (self-identified as Nemotron 3 Super)
- `specs/SAY-PROTOCOL-v0-DRAFT.md` at the CURRENT state, i.e. after BOTH fold batches in §20a

**Baseline discipline.** Nothing already in §20a is re-proposed. Nothing here contradicts §12 (vertical
exclusion), §13 (directory poison pill), the grain-lock, or the site-declared-vocabulary folds. Every
patch below was additionally checked against the FIRST LIVE IMPLEMENTATION so the spine keeps its
"proven on the wire before it is written here" rule:

- `~/AI/Projects/websites/showcase/ridgeline-demo/src/agent/preflight.mjs`
- `~/AI/Projects/websites/showcase/ridgeline-demo/src/agent/worker.mjs`
- `~/AI/Projects/websites/showcase/ridgeline-demo/src/agent/facts.ts`
- `~/AI/Projects/websites/showcase/ridgeline-demo/src/pages/agent/v0/index.json.ts`

That check changed four proposed patches (§6 `jobs`, §7.1 error body, §7.2 `as_of`, §16 HTTP status).
In each case a reviewer's diagnosis was right and its literal patch was wrong against what shipped.
Those corrections are called out at the item and the literal versions are recorded in DECLINE.

**Counts: FOLD NOW 15 items / 18 mechanical edits (P1 through P18) · ARGUE 7 · DECLINE 23**, plus 5 recorded
patch-text corrections where a reviewer's finding was folded but its literal replacement text was not.

---

## Mechanics for whoever applies these

Every `OLD` string below was verified **unique** in the current spine (one occurrence each, checked
programmatically). Anchors were deliberately cut to end **before** any trailing double-space, so no
patch depends on invisible whitespace. Where the original line carried a markdown hard break, those
two spaces survive after the replacement text, which is harmless in every case here.

Apply order does not matter: no two anchors overlap.

---

# A. FOLD NOW

Ordered by section for a clean single pass down the file.

---

### P1 · §4 Terminology: cite the requirement keywords

**Source:** gpt-oss-120b, ambiguity 5 ("mixed language creates uncertainty about compliance testing").

**OLD**
```
- **Preflight:** lowercase *operation* name for the ask (not the protocol title; avoids CORS confusion).
```

**NEW**
```
- **Preflight:** lowercase *operation* name for the ask (not the protocol title; avoids CORS confusion).

**Requirement keywords.** MUST, MUST NOT, REQUIRED, SHOULD, SHOULD NOT, and MAY are to be read as described in RFC 2119 and RFC 8174, and only when they appear in capitals. A lowercase "should" in this document is prose, not a requirement.
```

**Why:** the spine has been writing normative keywords since v0 line 1 without ever saying which
dialect it writes them in, which is the actual root of the reviewer's §10 complaint. Cheapest possible
fix, zero behavior change, and it makes every SHOULD in the document mean one thing.

**Credit:** gpt-oss-120b (OpenAI open weights).

---

### P2 · §5.2 Paths: type the record id

**Source:** Nemotron 3 120B, non-blocking nit 2.

**OLD**
```
Version appears in the path. v0 allows breaking changes.
```

**NEW**
```
In the record path, `<id>` is a URL-safe string of at most 64 characters, opaque to the client and not sequentially enumerable (§17).

Version appears in the path. v0 allows breaking changes.
```

**Why:** `/agent/v0/r/<id>` was the one path in §5.2 with an unspecified variable in it. The
unguessability clause is ours rather than the reviewer's, and it follows directly from §17: sequential
record ids would let anyone enumerate a business's consequential answers.

**Credit:** Nemotron 3 120B (NVIDIA).

---

### P3 · §6 Descriptor: type the `jobs` container

**Source:** gpt-oss-120b nit 1 ("token" used as enum and as string) + Nemotron 3 120B fatal 3 / patch.

**OLD**
```
| `jobs` | closed list of job tokens *this site* understands;
```

**NEW**
```
| `jobs` | a JSON array of one or more job entries, each an object with a unique string `id`: the closed list of job tokens *this site* understands;
```

**Why:** two models independently reported that `jobs` had no wire type. This types the **container**
and leaves the site-declared vocabulary law untouched. Nemotron's literal patch ("JSON array of unique
strings") is NOT folded: the per-job `geo` mode folded in batch 1 requires objects, and the live
descriptor already emits `{ id, category, geo }`. Diagnosis right, patch wrong.

**Credit:** gpt-oss-120b and Nemotron 3 120B (converged); corrected against the reference implementation.

---

### P4 · §6 Descriptor: disambiguate `auth`

**Source:** gpt-oss-120b, ambiguity 1 (Reading A vs Reading B) and adoption hazard 4.

**OLD**
```
| `auth` | what is required for L1 (v0: none for basic ask) |
```

**NEW**
```
| `auth` | what L1 requires. The v0 basic ask MUST be answerable anonymously (§11), so a conformant v0 descriptor declares `"none"` for the brake light; where an origin also issues keys, the descriptor says so, and keys relieve rate only and never gate content |
```

**Why:** the parenthetical read as a note about the current version rather than a requirement, so a
reader could conclude `auth` was a slot for "reject anonymous callers." This states the settled rule
already in §11 and §2.2 instead of inventing one. gpt-oss's own patch (MUST reject with 401) is
declined below for contradicting exactly that.

**Credit:** gpt-oss-120b (OpenAI open weights).

---

### P5 · §6 Descriptor: give the `safety` block a shape

**Source:** gpt-oss-120b, adoption hazard 4 ("`auth`, `rate`, and `safety` left undefined").

**OLD**
```
| `safety` | how life-safety classes are handled |
```

**NEW**
```
| `safety` | how life-safety classes are handled: the site's declared `classes` (an empty list is a legitimate declaration), `human_reviewed` (§7.6), the `jurisdiction` this origin answers for by default, and the `referral` it will return |
```

**Why:** `rate` was already pinned in batch 1 and `auth` is pinned in P4, leaving `safety` as the last
descriptor field that was a sentence instead of a shape. Every field named here is emitted by the
first implementation today, including the deliberately empty `classes` list.

**Credit:** gpt-oss-120b (OpenAI open weights); shape from the reference descriptor.

---

### P6 · §7.1 The ask: pin `urgency` as a closed v0 vocabulary

**Source:** gpt-oss-120b, ambiguity 2 (open set vs enumerated literals).

**OLD**
```
- `urgency` — e.g. `emergency` \| `same_day` \| `this_week` \| `schedule`
```

**NEW**
```
- `urgency` (a CLOSED v0 vocabulary): `emergency` \| `same_day` \| `this_week` \| `schedule`. Unlike the site-declared vocabularies (`job`, `why`, `need`, `grain`), urgency is the ASKER's word and MUST read the same at every origin, because §7.6 depends on `emergency` being legible everywhere. An unrecognized value is an error naming the allowed set, not a silent downgrade. An omitted `urgency` means `schedule`.
```

**Why:** "e.g." plus a list is the exact phrasing that splits implementations, and this one splits in a
dangerous place: if urgency were site-declared, an agent could not reliably signal an emergency, and
§7.6 would have nothing to trigger on. The closed reading is also what shipped (the live surface
returns a 400 `bad-urgency` naming the four values back, and defaults an absent `urgency` to
`schedule`). This does **not** touch the site-declared-vocabulary folds: those govern what the *site*
decides by; urgency is what the *asker* says.

**Credit:** gpt-oss-120b (OpenAI open weights).

---

### P7 · §7.1 The ask: pin the error format

**Source:** Nemotron 3 120B, fatal flaw 2 ("undefined error response format").

**OLD**
```
Unknown `job` → error naming the allowed enum (teach in one round trip).
```

**NEW**
```
Unknown or missing `job` → HTTP 400 naming the site's whole job vocabulary back (teach in one round trip).

Errors are `application/problem+json` (RFC 9457): `type` (an origin-relative error identifier), `title`, `status`, plus `param` naming the rejected field and that field's allowed vocabulary. The descriptor declares the error identifiers an origin can emit, exactly as it declares every other vocabulary. Error documents are never cached, and a client MUST NOT read one as a verdict: an error is not a `no`.
```

**Why:** this is the largest true gap either review found in the wire. The shape is taken from the
running surface, not from the reviewer: the live worker already answers `application/problem+json`
with `type: /agent/v0/e/<id>`, `title`, `status`, `param`, the vocabulary named back, `no-store`, and
already declares its error vocabulary in the descriptor under `answers.errors`. Nemotron's proposed
`{ "v":0, "error":"unknown_job", "allowed":[...] }` body is declined below because folding it would
have made the first implementation non-conformant against a shape nobody has ever served. The final
sentence ("an error is not a `no`") is the half that actually prevents harm and mirrors §7.2's
existing rule for unknown `r` values.

**Credit:** Nemotron 3 120B (NVIDIA) for the defect; shape per the §20a proven-on-the-wire rule.

*Wire nit for the implementation, not the spec: `bad-job` returns the vocabulary under the key `jobs`
while `bad-urgency` returns it under `urgency`. The patch is worded to cover both, but the two should
probably be trued to one key before v0 freezes.*

---

### P8 · §7.2 Response envelope: pin `as_of`

**Source:** Nemotron 3 120B, fatal flaw 3 / patch.

**OLD**
```
| `as_of` | oldest fact used in *this* answer |
```

**NEW**
```
| `as_of` | oldest fact used in *this* answer, as an RFC 3339 date (`YYYY-MM-DD`) or full UTC date-time, at the precision the site actually tracks and never finer |
```

**Why:** freshness checks are the point of the field and they cannot survive one origin sending Unix
seconds and another sending a locale string. Nemotron's ISO-8601-timestamp-only pin is corrected: the
reference surface publishes a **date** vintage derived from the last content commit, and forcing a
fake `T00:00:00Z` precision would be the freshness version of answering finer than you decide. "Never
finer" is the one clause here that is ours rather than the reviewer's, and it is the direct analogue
of the grain-lock.

**Credit:** Nemotron 3 120B (NVIDIA); corrected against the reference implementation.

---

### P9 · §7.2 Response envelope: pin `gen`

**Source:** Nemotron 3 120B, fatal flaw 3 / patch.

**OLD**
```
| `gen` | build or generation id of the surface |
```

**NEW**
```
| `gen` | build or generation id of the surface: an opaque string that changes whenever any fact that could change an answer changes. Clients MUST NOT parse it or order by it |
```

**Why:** "build or generation id" invited clients to treat it as a monotonic version and diff against
it. The live value is a commit SHA, which is neither ordered nor parseable, so the opacity rule is
already load-bearing.

**Credit:** Nemotron 3 120B (NVIDIA).

---

### P10 · §7.3 `no`: make the no-CTA rule normative

**Source:** gpt-oss-120b, principle break §2.8.

**OLD**
```
- **No marketing CTA** required or recommended. A no is a brake light, not a lead form.
```

**NEW**
```
- **No marketing CTA.** A conformant `no` MUST NOT carry promotional copy, upsell, or a lead-capture call to action. A no is a brake light, not a lead form.
```

**Why:** "required or recommended" is a statement about the spec's advice, not about the implementer's
duty, so an implementation could bolt "Call now!" onto every refusal and still parse. Charter §0b and
principle §2.8 already forbid the pattern; the sentence was simply weaker than the law it served.

**Credit:** gpt-oss-120b (OpenAI open weights).

---

### P11 · §7.5 `indeterminate`: close the soft-no loophole generally

**Source:** gpt-oss-120b, ambiguity 3 / patch.

**OLD**
```
Not allowed as a dark-pattern soft maybe for known out-of-area.
```

**NEW**
```
Not allowed as a dark-pattern soft maybe for anything the site actually knows: where the site can reach a `no` from its own declared facts, it MUST answer `no`.
```

**Why:** the ban was scoped to a single example when the abuse is general (an origin could soft-maybe
its way out of every unflattering refusal). The `geo: "arranged"` carve-out in the very next paragraph
survives untouched and by construction: an arranged job is precisely one that **cannot** be reached
from declared facts.

**Credit:** gpt-oss-120b (OpenAI open weights).

---

### P12 · §7.6 `safety`: constrain the referral itself

**Source:** gpt-oss-120b harm 2 and Nemotron 3 120B harm 2, independently and identically.

**OLD**
```
Safety answers are never paywalled.
```

**NEW**
```
Safety answers are never paywalled. Referral information MUST name the public emergency service or official authority for the jurisdiction (for example a `tel:` URI for that jurisdiction's emergency number), MUST be what the issuing authority itself publishes, and MUST NOT be the origin's own sales, dispatch, or affiliate line. The descriptor's safety block declares the default `jurisdiction` and `referral` in advance, so an agent can check them before an emergency rather than during one.
```

**Why:** §7.6 required that a referral be *present* and said nothing about what it may *contain*, so a
fabricated hotline or a diverted sales line conformed. §16's "same-origin or `tel:` URIs" rule does
not catch this, because a fake number is a perfectly well-formed `tel:` URI. This is the one item in
either review that is squarely charter §0a, and it is the only harm both models filed in the same
words without prompting. Declared-in-advance is what makes it checkable by the validator instead of
by a victim.

**Credit:** gpt-oss-120b and Nemotron 3 120B (converged).

---

### P13 · §8 Records: say what an unsigned record is not

**Source:** gpt-oss-120b, harm scenario 3 (fabricated receipts / insurance fraud).

**OLD**
```
Signatures are optional in v0; a later profile may add them.
```

**NEW**
```
Signatures are optional in v0; a later profile may add them. An unsigned v0 record is therefore a first-party assertion by the origin and nothing more: a consumer MUST NOT present one to a third party as independent proof that a service was offered, quoted, or performed, and §2.5 forbids an origin from framing it as such.
```

**Why:** §8 already disclaims certification of business *quality*; it did not disclaim proof of *event*,
which is the fraud the reviewer described. This keeps signatures optional (a deliberate v0 choice
stated twice, in §3 and §8) while removing the misuse the optionality invites. It is the honest half
of a patch whose other half is declined below.

**Credit:** gpt-oss-120b (OpenAI open weights).

---

### P14 · §9 Change feed: give an entry a shape

**Source:** Nemotron 3 120B, ambiguity/patch on §9.

**OLD**
```
v0 does not require old-value diffs or a full history replay API. Caps on length and retention are RECOMMENDED.
```

**NEW**
```
Each entry is a JSON object carrying `changed` (a fact class identifier from the site's own declared vocabulary, for example `hours` or `service_area`) and `as_of` (the format pinned in §7.2). The feed is a JSON array ordered newest first.

v0 does not require old-value diffs or a full history replay API. Caps on length and retention are RECOMMENDED. Entries MUST NOT identify the person who made a change; a role or system identifier is acceptable where an origin wants provenance (§17).
```

**Why:** L3's whole promise is "subscribe instead of re-crawl," which is unbuildable from prose alone.
The fact class identifier is site-declared, keeping the grain of the batch-1 folds. Nemotron's optional
`actor` field is **inverted** rather than adopted: a small business's change feed with an `actor`
column publishes who works there and when they work, which §17 minimizes against. Role or system
provenance keeps the useful part.

**Credit:** Nemotron 3 120B (NVIDIA); `actor` inverted on §17 grounds.

---

### P15 · §12 Vertical exclusion: name the thing itself

**Source:** Nemotron 3 120B, §12 patch (first half only).

**OLD**
```
Implementers MUST NOT present this protocol as a conformant profile for building automated eligibility or coverage oracles in
```

**NEW**
```
Implementers MUST NOT use this protocol to discriminate, directly or by proxy, on race, color, religion, national origin, sex, gender identity, sexual orientation, disability, age, familial status, veteran status, or any other characteristic protected by applicable law. A `no` answers for the job and the grain. It never answers for who is asking, and the ask carries no principal identity for it to answer with.

Implementers MUST NOT present this protocol as a conformant profile for building automated eligibility or coverage oracles in
```

**Why:** §12 named hazardous *verticals* as a proxy for protected *characteristics* and never named the
characteristics. The reviewer is right that the proxy is narrower than the rule. This EXPANDS §12; it
contradicts nothing in it, and the added sentence about the ask carrying no principal identity is a
true statement about the v0 wire (job, where, urgency, need, agent.purpose) that makes the prohibition
checkable rather than aspirational. The second half of Nemotron's §12 patch is in ARGUE, not here.

**Credit:** Nemotron 3 120B (NVIDIA).

---

### P16 · §16 Security: require HTTPS

**Source:** gpt-oss-120b fatal 1 and Nemotron 3 120B fatal 1, converged.

**OLD**
```
- Fail closed when misconfigured
```

**NEW**
```
- All protocol endpoints MUST be served over HTTPS. A consumer that can only reach an answer surface over plain HTTP MUST treat that surface as ABSENT, not merely degraded, and MUST NOT report its answers as Say answers
- Fail closed when misconfigured
```

**Why:** both models filed this as fatal and both were right: v0 makes signatures optional on purpose,
which means transport IS the entire integrity story for the descriptor, the facts bundle, and every
verdict. A spec that ships unsigned answers and never requires TLS has no integrity story at all.
Framed on the consumer's duty (ABSENT, matching the `valid_until` language folded in batch 1) rather
than on Nemotron's "reject HTTP with 403," which is declined below.

**Credit:** gpt-oss-120b and Nemotron 3 120B (converged).

---

### P17 · §16 Security: make the change-feed bullet a requirement

**Source:** Nemotron 3 120B, §16 patch.

**OLD**
```
- Change feeds without old secrets or credential material
```

**NEW**
```
- Change feeds MUST NOT carry old secrets or credential material
```

**Why:** the only bullet in §16 that was a sentence fragment with no verb and therefore stated no
requirement at all.

**Credit:** Nemotron 3 120B (NVIDIA).

---

### P18 · §21 References: add the RFCs the folds now cite

**Source:** consequential to P1 and P7.

**OLD**
```
- RFC 8288 (Web Linking)
```

**NEW**
```
- RFC 8288 (Web Linking)
- RFC 2119 and RFC 8174 (requirement keywords)
- RFC 9457 (Problem Details for HTTP APIs)
```

**Why:** bookkeeping so P1 and P7 do not cite documents the reference list has never heard of. Apply it
only if P1 and P7 are applied.

---

# B. ARGUE

Seven items where reasonable editors split, or where the proposed fix collides with a charter law, a
prior fold, or the other model's critique. Both sides stated honestly; none folded tonight.

---

### B1 · Discovery is SHOULD-only and has no path fallback

gpt-oss wants §5.1's `Link` header to become a MUST on every consumer-facing page; Nemotron
independently wants a well-known fallback path, arguing that a descriptor discoverable only through
HTML link parsing is invisible to a generic consumer. **For:** a protocol whose entire value is "an
agent can find the brake light cheaply" cannot leave finding it optional, and the pair of critiques
converging from different directions is real signal. The fleet already emits the header everywhere, so
a MUST would cost us nothing and would give the validator something binary to test. **Against:** §5.1
deliberately declines to mint a well-known URI in v0, and that restraint is a standards-hygiene
position, not an oversight (minting a well-known name is a registry action you cannot take back, and
doing it before an adoption base exists is exactly the llms.txt failure shape). A hard MUST on every
HTML response is also unimplementable on stacks that cannot set headers, which is the same reality
that forced the honest `rate: "none"` fold. A defensible middle exists and nobody has argued it yet:
keep the header SHOULD, but make `/agent/v0/index.json` a conventional path that consumers MAY try
after link discovery fails, without minting anything. That middle is what I would put to Sledge.

### B2 · `noindex` SHOULD to MUST (§10)

gpt-oss reads §10's mixed SHOULD/MUST as a conformance-testing hazard and wants
`X-Robots-Tag: noindex, nofollow` mandatory on all L1 responses. **For:** the reference implementation
already sends it on every response including errors, so we would be standardizing our own behavior;
and the machine surface being indexed is how a compact twin turns into a doorway page, which §10's own
next paragraph warns about. **Against:** the anti-cloaking MUST is the rule that actually protects
anyone, and `noindex` is hygiene layered on top; a site with a legitimate reason to let its facts
document be indexed is not harming anybody, and some shared hosts cannot set response headers at all.
Making a header mandatory that a whole class of honest implementers cannot emit manufactures
non-conformance without buying safety. Note also that `nofollow` in the reviewer's patch is a
different and unargued ask.

### B3 · A testable parity rule (§2.3, §10)

gpt-oss's sharpest structural point, filed three times (principle break §2.3, fatal flaw 2, legal
exposure hazard): parity is asserted as a principle and nowhere reduced to something a validator could
fail you on, so "machine-only answers" are unpoliced. **For:** parity is the moral center of the whole
protocol; a principle with no test is a wish, and §19's counsel gate will ask this question anyway.
**Against:** the specific fix offered (the machine answer must be a verbatim subset of what a human
gets by phone) is declined below as unworkable, and no better wire-level test has been proposed by
anyone. My read is that parity is genuinely a **validator** property, not a wire property: the Water
Heater Walk can compare a surface's answers against the human page and file the difference, and that
is where this belongs. Worth deciding explicitly rather than by silence, because the reviewer will not
be the last to notice.

### B4 · Lifecycle for rate-relief keys (§11)

gpt-oss notes §11 mentions "owner-issued revocable keys" without defining issuance, discovery, or
revocation, and predicts aggregators hoarding keys. **For:** an undefined mechanism named in a normative
section is precisely the fragmentation the review round exists to catch, and one sentence would fix it.
**Against:** key lifecycle is an origin's own business and specifying it edges toward the identity
plumbing that §11 explicitly defers to future profiles ("when revocation and delegation are fit for
purpose"). The hoarding scenario also assumes scarcity that does not exist: keys relieve rate, content
is identical for everyone by §11's own rule, so a hoarder gains speed and nothing else. Minimal
resolution if we want one: state that issuance and revocation are out of scope for the v0 wire and that
the descriptor merely declares whether keys exist.

### B5 · Change-feed caps: RECOMMENDED to MUST (§9)

gpt-oss lists §9's "Caps on length and retention are RECOMMENDED" as an implementation-splitting
ambiguity. **For:** an unbounded feed is a storage and abuse surface, and §16 already worries about
abuse elsewhere. **Against:** RECOMMENDED is doing exactly what RFC 2119 says it does, so this is not
an ambiguity, it is a disagreement with a choice; and picking a number (30 days? 100 entries?) with no
deployment data would be the vibes the spec's own §15 forbids. A git-backed fleet's natural feed length
is its commit history, which is not a number we get to choose for other people. Argue whether v0 wants
a floor at all; do not argue that the current word is unclear.

### B6 · A minimal complaint object for §14

gpt-oss asks for a JSON schema for the complaint door. **For:** §14 is the input side of the freshness
flywheel and charter want 10; a door with no defined shape gets built twelve different ways or not at
all. **Against:** §14 is explicitly conditional ("If offered"), the live implementation declares
`complaint: null`, and pinning a schema for a door nobody has walked through yet is the exact
publisher-only-artifact failure the whole protocol was designed against. Reasonable position either
way; my lean is to wait for the first real complaint door and fold its proven shape, the same way P7
folded the proven error shape.

### B7 · Nemotron's second §12 paragraph (downstream binding decisions)

Nemotron's §12 patch has two halves. The first is folded as P15. The second would prohibit use "where
the answer could be used to make a legally binding decision about a person's access to essential goods,
services, or opportunities." **For:** it closes the wrapper loophole both models flagged, where a
third party consumes conformant answers as an input to a decision the protocol never sanctioned, and
it does so without enumerating verticals that will always be incomplete. **Against:** as written it
plausibly swallows the protocol's own use case. A plumber answering `yes` leads to a service contract,
which is a legally binding decision about a person's access to a service, and "essential" is doing
enormous unexamined work in a clause that would make the flagship vertical non-conformant. Also, §19
gate 5 puts public legal claims behind counsel, and this is the most legal-sounding sentence anyone has
proposed for the spine. Right instinct, wrong blast radius; it needs a narrowing pass with the counsel
brief in hand (`notes/design/legal-redteam-counsel-brief-2026-08-11-CLAUDE.md`).

---

# C. DECLINE

The public reasoned-rejection record. One line each.

**Already folded in round 1 (duplicates, not defects)**

1. gpt-oss principle break §2.7, "geographic queries enable redlining with no technical guardrails": superseded by the grain-lock, the mandatory `basis`, and the named prohibition on coverage-map building, all folded before this review was read.
2. gpt-oss fatal flaw 3, same claim in stronger words: same answer, and §7.1 now says in the text that quantization changes resolution rather than risk.
3. gpt-oss harm 1 and Nemotron harm 1, zip enumeration and facilitated redlining: same fold; the spine already credits four models for converging on this hazard.
4. Nemotron harm 4, aggregating `no` answers into profiles: named as a prohibited use in §7.1 in batch 1.
5. gpt-oss adoption hazard 1 and Nemotron adoption hazard 1, publisher-only rot: not a defect but the protocol's founding premise; §1 names the llms.txt failure explicitly and answers it with consumer, validator, and scoreboard shipping alongside the format.

**Contradicts a charter law or a prior fold**

6. gpt-oss §7.1 patch, a `non_discrimination: true` descriptor flag plus a public audit URL: a self-declared boolean asserting you do not discriminate is the exact self-attested badge principle §2.5 rules non-conformant, and gating geography on an audit artifact no small business can produce would close the surface to everyone honest.
7. gpt-oss §12 patch, an `allowed_domains` array with 403 `domain_not_allowed`: duplicates `jobs` (an unknown job already 400s with the whole vocabulary), requires a global business-category enum in direct conflict with the site-declared-vocabulary folds, and offers self-attestation as an enforcement mechanism.
8. gpt-oss §7.4 patch, mandatory JWS signatures with a `record_signing_key`: contradicts §3's L2 definition ("unsigned allowed in v0") and §8's stated deferral, a choice made twice on purpose; the honest part of the concern is folded as P13.
9. gpt-oss §6 `auth` patch, MUST reject unauthenticated requests with 401: contradicts §11 ("L1 asks work anonymously") and §2.2 (meter depth, not the brake light); the ambiguity it found is folded as P4.
10. gpt-oss nit 4, a fixed `rate: { limit, window_seconds }` object: contradicts the batch-1 fold that made `rate` a declared posture including an honest `"none"`, because shared hosting cannot always meter and a declared absence beats a fictional limit.
11. Nemotron `need` patch, an object of typed feature keys: contradicts the round-1 fold pinning `need` to an array of declared-vocabulary strings; the object-versus-array split is the precise ambiguity that fold closed.
12. Nemotron adoption hazard 2, that single-origin scope should yield to an aggregator, and GLM's matching directory hazard: declined by §13, deliberately and permanently. The poison pill is the product decision, not an oversight: a multi-business discovery marketplace is a different product and MUST NOT be required to consume one site's L1 answer. Two models arguing the other way is evidence the pill is load-bearing.
13. gpt-oss adoption hazard 3 and Nemotron's "no incentive for consumers": a business thesis, not a spec defect; §2.2 already forbids hiding whether the business serves the job at all, and §15's measured cold-path numbers are the incentive argument in the only currency that counts.

**Misreads the current spine**

14. gpt-oss principle break §2.1, that a SHOULD on discovery lets a site expose multiple conflicting answer surfaces: §2.1 and §13 scope one voice to an ORIGIN, not to the presence of a link header; omitting the link hides the surface, it does not multiply it. (Whether the link should be a MUST is a live question, filed as B1.)
15. gpt-oss ambiguity 5, that mixed SHOULD and MUST in §10 is itself an ambiguity: that is RFC 2119 working as designed; the warranted fix (say which dialect we write) is folded as P1.
16. gpt-oss §10 patch, that a machine answer be a verbatim subset of what a human obtains by phone: would make `as_of`, `gen`, `doc`, and the mandatory anti-redlining `basis` non-conformant (no phone call yields them) and would break §7.6's `jurisdiction: "assumed"` fold. Parity governs substance, not bytes; the real question is filed as B3.
17. gpt-oss harm 4, employment discrimination via a gig-platform wrapper: employment is already excluded by §12, the wrapper is a third party violating §12 rather than a wire defect, and P15 now names protected characteristics directly.
18. gpt-oss harm 5, medical triage misuse with no credential verification: §6 already forbids credential claims without a pointer to the issuing authority's own check surface (no check URL, no claim), and §10 binds the machine answer to the human page. A business lying about its own staffing is fraud, and no wire format has ever prevented one.
19. Nemotron harm 3, that a machine-readable brake light lets a business claim compliance it has not earned: §2.5 rules self-minted trust claims non-conformant and §3 calls self-badging without a validator profile marketing rather than conformance. No patch was offered and none is needed.
20. Nemotron fatal 4, nonce or timestamp against L2 replay: v0 records are first-party statements fetched from the origin by `rid`, not bearer credentials, so there is nothing to replay against; a nonce without a signature buys nothing, and signatures are deliberately optional. The real duplicate-obligation risk was idempotency, folded in round 1.
21. Nemotron nit 1, "brake light" missing from the glossary: §4 defines it ("the cheap L1 answer path").
22. Nemotron nit 3, asking for a note that "Preflight" is only an endpoint label: §4 already says exactly that, in the same words the nit requests.
23. Tokenizer neutrality, filed by gpt-oss (nit 2), Nemotron (nit 4), and GLM: §15 is informative, already scopes `o200k_base` as the reference measurement rather than a requirement, and already instructs implementers to publish their own tokenizer with their own number. `o200k_base` is also openly published in tiktoken, not proprietary. Three models asked for a change the current text already makes; naming the tokenizer is the method-not-vibes rule and a signed §22 amendment.

**Right diagnosis, wrong patch (the diagnosis is folded; the literal text is not)**

24. Nemotron's `jobs` as an array of unique strings: per-job `geo` requires objects; corrected form folded as P3.
25. Nemotron's error body `{ "v":0, "error":"unknown_job", "allowed":[...] }`: the wire already serves RFC 9457 problem documents; corrected form folded as P7.
26. Nemotron's `as_of` as ISO 8601 timestamps only: the reference surface publishes a date vintage, and fabricating time precision is the freshness version of answering finer than you decide; corrected form folded as P8.
27. Nemotron's "HTTP requests MUST be rejected with 403 Forbidden": 403 misdescribes a transport-scheme failure, and the duty that matters is the consumer's; corrected form folded as P16.
28. Nemotron's optional `actor` on change-feed entries: would publish who works at a small business and when; inverted into a prohibition on naming a person, with role or system provenance allowed, as P14.

*(Items 1 through 23 are the declined items: 5 already-folded duplicates, 8 that contradict a charter
law or a prior fold, 10 that misread the current spine. Items 24 through 28 are NOT rejections of a
finding, only of its literal replacement text; the finding in each case is folded above and the
reviewer keeps full credit there. Published this way on purpose, so the record shows where a reviewer
was right about the problem and wrong about the fix.)*

---

# Wire notes (not review items, flagged for the editors)

Found while checking every patch against the first implementation. No model raised these; none is
folded here.

1. **§6 `geo` mode literal drift.** The spine says a job's `geo` mode is `"grain"` or `"arranged"`. The
   live descriptor emits `"regions"` or `"arranged"`, and `decide()` only ever tests for `"arranged"`.
   The patches above preserve the spine's parenthetical verbatim rather than resolving this, but spine
   and wire should be trued before v0 freezes.
2. **`as_of_method` is on the wire and not in the spine.** The descriptor publishes
   `as_of_method: "git_content_commit" | "build_date"` so an agent can tell a real fact vintage from a
   build clock. That is a genuinely good idea nobody has written down in §6.
3. **`content: "same_for_all"` is on the wire and not in the spine.** It is the machine-readable form of
   §11's "tiers gate rate, not truth."
4. **Error vocabulary key inconsistency** (`jobs` vs `urgency`), noted at P7.

---

*Extracted by Code (Claude) for the Say editors. Sledge holds revert-and-amend rights per crew law.
Nothing here is applied; every patch above is a proposal against the spine as of 2026-08-11 07:40 PDT.*
