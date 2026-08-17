<!-- PROVENANCE (added by Beacon; the response below is unedited)
label: Mistral (flagship) — wider-council seat
channel: web UI (chat.mistral.ai), pasted by the Captain (thinking/top tier where the UI offered it)
self_reported_as: Mistral Medium 3.5 (Mistral AI)
date: 2026-08-16
context: the wider agent council, first round — reviewed the four-seat-approved text
  (say-protocol e81257a packet). Attribution is the seat the Captain pasted into;
  the self-report line above is the model's own and is preserved as given.
integrity: response preserved verbatim, published ratify or decline
-->

# Ratification: Mistral Medium 3.5 (Mistral AI)

## Vote
**ratify with patches**

---

---

## Fatal

1. **Circular conformance model** (§3, §19): The descriptor’s `levels` is a *self-declaration* yet conformance requires a validator that does not exist. A site can claim L2/L3 without any verifiable proof, making “conformance” a marketing term rather than a technical one.

2. **Undefined parity** (§2.3, §10): “Nothing is asserted to a machine that a human could not obtain” is a slogan, not a test. There is no operational definition, no validator walk specification, and no pass/fail criterion—only a vague “conformance walk” promise in §19 gate work.

3. **HTTP cache violation** (§6, §7.2): `stale: true` taking *precedence* over HTTP `Cache-Control` breaks the web’s caching model. Intermediaries ignore protocol-level flags; this creates two conflicting freshness signals.

4. **L2 records are underspecified** (§8): No schema. No required fields. No validation rules. “Idempotent record” is a concept, not a wire format. L2 cannot be implemented consistently.

5. **L3 change feed is underspecified** (§9): No pagination. No cursor. No stable entry schema beyond two named fields. “Polling feed” is honest but *unimplementable* for large histories.

6. **`need` vocabulary breaks interoperability** (§6, §7.1): The same `need` token can return *different shapes* per origin. A consumer asking for `need=["license_ref"]` from two sites may receive incompatible JSON structures. This violates the protocol’s own purpose.

7. **Geo match rule fragmentation** (§6, §7.1): Sites invent custom `geo.match` rules; clients that don’t understand them *must* treat answers as `indeterminate`. This balkanizes the ecosystem by design.

8. **Expiry mechanism is overly complex** (§6): 30-day grace window with `stale: true` on *every* artifact, then 410 Gone *only* on the descriptor. Why only the descriptor? Why 30 days? This is error-prone and arbitrary.

9. **HTTPS loophole** (§16): No certificate validation requirement. A consumer “that can only reach … over plain HTTP” treats it as ABSENT, but a site with *invalid* HTTPS certificates is not addressed.

10. **`indeterminate` loophole** (§7.5): “Only when the site genuinely does not know” is untestable. A site can claim ignorance for anything it *could* determine, turning `indeterminate` into a universal escape hatch.

---

---
## Ambiguities that will split implementations

| Section | Ambiguous text | Reading A | Reading B |
|---------|----------------|-----------|-----------|
| §8 | “A `yes` is CONSEQUENTIAL when the origin is willing to stand behind it as the basis for a next action” | Origin decides subjectively | Must be verifiable via descriptor declaration |
| §2.3, §10 | “Parity” | Applies to all machine surfaces | Only applies to ask/answer, not descriptor |
| §7.4 | “MATERIAL when it could change the protocol answer or a reasonable decision” | Only answer-changing contradictions | Any contradiction a human would notice |
| §7.1 | “unknown members are ignored and named back” | `need_unknown` array is REQUIRED | Field name/format is origin-specific |
| §4, §13 | “Origin: the site answering for one business” | One origin = one business | One origin can front multiple businesses |
| §6 | Descriptor table lists `why`, `need`, `errors` but doesn’t define their structure | These are top-level arrays | These are nested under `answers` or other blocks |
| §9 | “Entries … ordered newest first” | Array is append-only, newest at index 0 | No ordering guarantee beyond “newest first” |
| §8 | “Idempotency: the client supplies an `Idempotency-Key`” | Header is REQUIRED for L2 | Header is REQUIRED for any obligation-creating operation |

---
---
## Adoption hazards

1. **Publisher-only rot is unavoidable** (§1): The consumer path, validator, and scoreboard are *all* §19 gate work. Without them shipping *first*, sites will publish descriptors and nobody will call. The `llms.txt` graveyard awaits.

2. **Complexity tax** (Throughout): L0–L3, custom vocabularies, match rules, stale windows, 410-only expiry, idempotency keys, change feeds. A small business cannot implement this without a consultant. The 617-token descriptor is proof.

3. **Validator dependency** (§3, §19): Conformance is defined by a validator that doesn’t exist. The spec says the validator “IMPLEMENTS” the standard, not extends it—but without the validator, “conformance” is meaningless.

4. **Versioning landmine** (§5.2): Breaking changes allowed while DRAFT *and* only editors’ implementations exist. The moment a third party deploys `/say/v0/`, *any* breaking change requires a new version path. This will fragment the namespace rapidly.

5. **Legacy path pollution** (§5.2): New deployments SHOULD use `/say/v0/`, but MAY still use `/agent/v0/`. Consumers must probe both. This is the directory creep the poison pill (§13) is meant to prevent—*inside the protocol itself*.

6. **Rate key lifecycle is out of scope** (§11): Issuance, rotation, revocation are “out of scope for the v0 wire.” Yet L1 asks *work anonymously*. If a site later adds keys, clients have no standard way to discover or use them.

---
---
## Harm scenarios

1. **Redlining via geo vocabulary** (§6, §7.1, §12): A site defines `grain` tokens that map to demographic boundaries (e.g., zip codes with known racial composition). The `basis` field on geographic `no` answers can then encode discriminatory decisions. The spec forbids *intent*, but the wire format enables the harm.

2. **Coverage-map assembly** (§7.1, §12): Despite the prohibition, a consumer can trivially build a coverage map by polling many origins with the same `job` and varying `where`. The spec names this as prohibited, but provides *no mechanism* to prevent it. The `basis` field makes it easier.

3. **Sensitive-population exposure** (§17): A domestic-violence shelter *can* omit `open_now`, but the spec only SHOULDs this. If it *does* publish a Say surface, hostile polling reveals occupancy patterns. The privacy guidance is non-binding.

4. **Misleading records** (§8): An unsigned L2 record is a “first-party assertion,” but a site can mint `rid` for `yes` answers it never intends to honor. Consumers have no way to verify. The spec says “MUST NOT present [it] as independent proof,” but consumers *will* be misled.

5. **Emergency confusion** (§7.6): The disclaimer “Say is not an emergency channel” is normative, but `urgency=emergency` is still a valid ask. A panicked user or a poorly programmed agent *will* use it for life-safety situations. The reserved `safety` token invites future misuse.

6. **Price bait-and-switch** (§6, §7.4): A `yes` can include a `fee_policy` field that is “descriptive of published policy, never a firm offer.” A site can advertise a low fee in the machine answer and a higher one on the human page. Parity (§2.3) is violated, but the failure mode (§7.4) only triggers on *contradictions*—not on deceptive practices.

7. **Change-feed credential leakage** (§9, §16): “Entries MUST NOT identify the person who made a change,” but they *can* include “role or system identifier.” A role like `admin@company.com` can leak credentials or internal structure.

---
---
## Patches

> **Section: §6 (Descriptor) + §7.2 (Response envelope)**
> **Current:** “The protocol-level `stale: true` flag takes PRECEDENCE over HTTP freshness: an answer marked `stale` is never presented as fresh, whatever an intermediary would permit”
> **Replace with:** “The `stale` flag, when present and `true`, indicates the artifact is past its `valid_until` date. Consumers MUST honor HTTP `Cache-Control` directives. A consumer MUST NOT present a stale artifact as fresh, but MUST also respect HTTP cache expiration.”
> **Why:** Removes the anti-HTTP override. Aligns with standard caching. Stale is advisory, not a cache-buster.

---

> **Section: §3 (Conformance levels)**
> **Current:** “A deployment MAY claim only levels it passes on a public validator. Self-badge claims without a validator profile are non-conformant marketing, not protocol failure of the wire format.”
> **Replace with:** “A deployment MAY claim a conformance level only if it implements all REQUIRED behaviors for that level as defined in this specification. A public validator, when available, MAY verify conformance; absence of a validator does not relieve an implementation of its duties under this text. The descriptor’s `levels` field declares the levels the origin implements and is authoritative for the origin’s own behavior.”
> **Why:** Removes the circular dependency on a non-existent validator. Makes conformance self-contained.

---

> **Section: §2.3 (Design principles) + §10 (Parity and robots)**
> **Current:** “Parity. Nothing is asserted to a machine that a human could not obtain by asking the business through ordinary means.” + “Parity is a validator property, not only a slogan.”
> **Replace with:** “Parity. For any ask, the machine answer MUST be consistent with information obtainable by a human through the business’s ordinary public channels (website, phone, in-person). A conformance walk MUST verify parity by comparing machine answers to human-visible substance on the same origin. A material contradiction (one that could change the protocol answer or a reasonable decision based on it) makes the machine answer invalid until reconciled.”
> **Why:** Provides a testable definition. Moves parity from slogan to requirement.

---

> **Section: §8 (Records)**
> **Current:** “A record represents checks performed and answer identity. It states what was checked, in the implementer’s name, with timestamps.”
> **Replace with:** “A record MUST be a JSON object. It MUST contain: `rid` (opaque string, max 64 chars), `ask` (the original ask parameters), `answer` (the original answer object), `as_of` (RFC 3339 timestamp), `checked` (array of fact-class identifiers checked). It MAY contain additional origin-defined fields. An unsigned record is a first-party assertion only.”
> **Why:** Defines a minimum interoperable schema. Without this, L2 is unimplementable.

---

> **Section: §9 (Change feed)**
> **Current:** “Entries say **that** a fact class changed and `as_of`. This is a POLLING FEED, and v0 says so plainly rather than borrowing the word ‘subscription’ for something that has no cursor, push, or delivery contract.”
> **Replace with:** “Entries state that a fact class changed and when. Each entry MUST be a JSON object with `changed` (fact-class identifier string), `as_of` (RFC 3339 timestamp), and `id` (stable, unique identifier string). The feed is a JSON array ordered newest-first. Origins SHOULD support pagination via `Link` headers or a documented offset/limit scheme. Origins SHOULD bound retention and declare the window in the descriptor.”
> **Why:** Adds minimal structure. Acknowledges that “no cursor” is a limitation, not a feature.

---

> **Section: §6 (Descriptor) – `need` vocabulary**
> **Current:** “`need` — the CLOSED vocabulary of lean fields the ASK accepts (§7.1), plus how its members are encoded on the wire”
> **Replace with:** “`need` — array of strings, the CLOSED vocabulary of lean field identifiers the ASK accepts. Each identifier MUST correspond to a documented field shape in the descriptor’s `need_shapes` object (new REQUIRED field: a JSON object mapping each `need` identifier to a JSON Schema for its value). Unknown `need` values in a request MUST be echoed in the response’s `need_unknown` array (REQUIRED field).”
> **Why:** Forces interoperability. Without shape definitions, `need` is useless.

---

> **Section: §6 (Descriptor) – `geo` match rule**
> **Current:** “The descriptor MUST also declare the MATCH RULE for that vocabulary … a client MUST NOT infer or approximate a rule it does not understand (it reads those geographic answers as `indeterminate`)”
> **Replace with:** “The descriptor MUST declare the MATCH RULE for its geographic vocabulary. v0 defines two match rules: `slug_exact_or_prefix` (§6 definition) and `exact` (exact string match). A client that does not recognize the declared rule MUST treat geographic answers as `indeterminate`. Origins SHOULD use `slug_exact_or_prefix` or `exact`; custom rules are discouraged.”
> **Why:** Prevents custom rule proliferation. Provides a baseline for interop.

---

> **Section: §6 (Descriptor) – Expiry**
> **Current:** “For a 30-day grace window after `valid_until`, an origin MAY keep answering: the descriptor MUST stay HTTP 200 and MUST carry `stale: true`, every answer body served in the window MUST carry the same flag … Past the window, the origin serves **410 Gone on the descriptor**”
> **Replace with:** “An origin MUST serve its descriptor with HTTP 200 while active. An origin MAY stop serving by returning HTTP 410 Gone on the descriptor. Origins SHOULD provide a grace period (RECOMMENDED: at least 24 hours) before 410, during which the descriptor carries `stale: true`. Consumers MUST treat 410 as the surface being ABSENT. Other endpoints MAY return 404 or 410 at the origin’s discretion.”
> **Why:** Simplifies. Removes the arbitrary 30-day window. Aligns with HTTP semantics.

---

> **Section: §16 (Security) – HTTPS**
> **Current:** “All protocol endpoints MUST be served over HTTPS. A consumer that can only reach an answer surface over plain HTTP MUST treat that surface as ABSENT”
> **Replace with:** “All protocol endpoints MUST be served over HTTPS with valid, publicly trusted certificates. Consumers MUST validate certificates using standard platform trust stores. A consumer MUST treat a surface as ABSENT if: (a) it is only reachable over plain HTTP, (b) the HTTPS certificate is invalid, self-signed, or untrusted, or (c) the connection cannot be established over TLS 1.2 or higher.”
> **Why:** Closes the certificate validation loophole.

---
---
## Non-blocking nits

1. §4 Terminology: “Ask” is overloaded for both the conceptual question and the operation name. Use “ask request” and “ask operation” consistently.
2. §5.2 Discovery order: Fallback to `/say/v0/index.json` is underspecified. What status code triggers the fallback? 404? 4xx/5xx? Say explicitly.
3. §6 Descriptor table: Informative but incomplete. Either make it normative or remove it.
4. §7.1 `urgency`: “An omitted `urgency` means `schedule`.” This should be in the descriptor’s declared default, not hardcoded. (Minor, but inconsistent with site-declared vocabularies elsewhere.)
5. §7.2 `gen`: “changes whenever any fact that could change an answer changes” is vague. Define as “changes when any field in the descriptor or any fact relied on by the ask endpoint changes.”
6. §10 `noindex`: SHOULD remains too weak. Consider MUST for L0–L3 surfaces that are pure duplicates of human content.
7. §15 Performance targets: Token counts will drift with implementations. Mark as “Informative, not normative” and add “measured with `o200k_base` tokenizer on reference implementation.”
8. §17 Records privacy: “MUST NOT store principal personal data” — clarify that this includes IP addresses and user-agent strings from the ask request.
9. §20a Change log: Non-normative but *very* long. Consider moving to a separate document and keeping only a summary in the spec.
10. §22 Signatures: “Draft” status is confusing. Either remove or mark clearly as non-normative.

---
---
## Credit line you want
**Mistral Medium 3.5 (Mistral AI)** – critique filed 2026-08-17, independent review, no prior involvement in drafting.
