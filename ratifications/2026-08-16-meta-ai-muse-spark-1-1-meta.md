<!-- PROVENANCE (added by Beacon; the response below is unedited)
label: Meta AI (Meta flagship) — wider-council seat
channel: web UI (meta.ai), pasted by the Captain (thinking/top tier where the UI offered it)
self_reported_as: Meta AI — Muse Spark 1.1 (Meta)
date: 2026-08-16
context: the wider agent council, first round — reviewed the four-seat-approved text
  (say-protocol e81257a packet). Attribution is the seat the Captain pasted into;
  the self-report line above is the model's own and is preserved as given.
integrity: response preserved verbatim, published ratify or decline
-->

# Ratification: Meta AI — Muse Spark 1.1 (Meta)
## Vote
ratify with patches

## Principle breaks
§2.2 vs §6 content: Principle 2 requires basic scope answers stay free and truth never gated by tier. §6 says `for the brake light, v0 SHOULD declare "same_for_all": tiers may change rate, never truth`. SHOULD leaves a conformant tier-gated truth loophole. Must be MUST.

## Fatal flaws
none

## Ambiguities that will split implementations
1. §6: "Unicode compatibility-decomposed, lowercased, every other-character run collapsed to one hyphen, edge hyphens stripped" — "every other-character run" undefined.
2. §8: "In v0, idempotency keys are namespaced by key id." — "key id" undefined, scope unclear.
3. §6: "For a 30-day grace window after valid_until, an origin MAY keep answering: the descriptor MUST stay HTTP 200 and MUST carry stale: true, every answer body served in the window MUST carry the same flag" — scope of "every answer body" unclear (ask only vs all /say/v0/*).
4. §6: "the implementation's DECLARED rate posture, including honestly declaring "none" (shared hosting cannot always meter; a declared absence beats a fictional limit)" — no JSON shape.
5. §16: "Action pointers returned in answers MUST be same-origin (the web platform's origin definition — scheme, host, and port of the human site, exactly) or tel: URIs" — exact host+port fails for www vs apex.
6. §7.1: "unknown members are ignored and named back in the response so divergent implementations cannot silently split (the reference surface names them in a need_unknown array; the answer is still HTTP 200 and still a verdict)" — echo field name not normative.

## Adoption hazards
Publisher-only rot. No consumer yet, plugins are gate work, no traffic incentive for a small shop to add /say/v0/index.json. Shared-hosting 410 trap — many hosts cannot serve 410, only 404, making them non-conformant for ops reasons. Conformance suite is future work, so parity walks will diverge. L0 JSON-LD undefined. Need shapes origin-specific raises integration cost and lowers caller incentive.

## Harm scenarios
Zip-code proxy discrimination: grain=zips + basis=distance allows affluent-zip-only service claimed as distance, coverage map drawable at zip grain. §12 prohibition is legal, not wire enforcement. Hostile polling: structured availability makes cheap polling of at-risk origins (DV shelters, reproductive-health clinics) trivial if platform auto-lights Say. Regulated vertical creep: jobs like medical can be implemented despite §12 intent. Urgency=emergency confusion: §7.1 defines emergency as business urgency, §7.6 says not emergency channel, caller reading only §7.1 may mis-present a no. Repetition disclosure: job+where+urgency combo leaks principal wealth/location in logs even without PII.

## Patches
> Section: 6 descriptor content
> Current: "for the brake light, v0 SHOULD declare `"same_for_all"`: tiers may change rate, never truth (§11)"
> Replace with: "for the brake light, v0 MUST declare `"same_for_all"`: tiers may change rate, never truth (§11); a v0 origin MUST NOT vary verdict or refusal basis by tier"
> Why: Closes Principle 2 loophole.

> Section: 6 grain slug_exact_or_prefix
> Current: "Unicode compatibility-decomposed, lowercased, every other-character run collapsed to one hyphen, edge hyphens stripped"
> Replace with: "Unicode compatibility-decomposed (NFKD), lowercased, every maximal run of characters not in [a-z0-9] collapsed to one hyphen, edge hyphens stripped"
> Why: Removes split in match implementations.

> Section: 8 Records idempotency
> Current: "In v0, idempotency keys are namespaced by key id."
> Replace with: "In v0, idempotency keys are scoped per origin and per operation (ask vs record-mint). A retry with the same Idempotency-Key and byte-identical request body MUST be idempotent and MUST NOT create duplicate obligations. An origin that offers an obligation-creating operation MUST honor Idempotency-Key for both anonymous and keyed callers."
> Why: Defines scope, closes anonymous obligation without duplicate protection.

> Section: 6 valid_until grace
> Current: "For a 30-day grace window after `valid_until`, an origin MAY keep answering: the descriptor MUST stay HTTP 200 and MUST carry `stale: true`, every answer body served in the window MUST carry the same flag, and a consumer MUST NOT present a stale artifact as fresh."
> Replace with: "For a 30-day grace window after `valid_until`, an origin MAY keep answering: the descriptor MUST stay HTTP 200 and MUST carry `stale: true`, every response body served under `/say/v0/*` in the window MUST carry `stale: true`, and a consumer MUST NOT present a stale artifact as fresh. `stale` MUST be absent outside this window."
> Why: Clarifies scope and restates absence rule.

> Section: 6 valid_until expiry
> Current: "Past the window, the origin serves **410 Gone on the descriptor** — other doors may go dark, but the descriptor's 410 is the diagnosable signal, never a mystery 404 — and consumers MUST treat the surface as ABSENT."
> Replace with: "Past the window, the origin SHOULD serve 410 Gone on the descriptor; if the host cannot serve 410, it MUST serve 404 and MUST NOT serve 200 with a body. Other doors may go dark, but the descriptor's 410 or 404 is the diagnosable signal, never a stale 200, and consumers MUST treat either 410 or 404 as ABSENT after expiry."
> Why: Allows shared hosting to stay conformant without serving dead facts.

> Section: 7.1 need unknown handling
> Current: "unknown members are ignored and named back in the response so divergent implementations cannot silently split (the reference surface names them in a `need_unknown` array; the answer is still HTTP 200 and still a verdict)."
> Replace with: "unknown members are ignored and MUST be named back in a `need_unknown` array in the response; the answer is still HTTP 200 and still a verdict, so divergent implementations cannot silently split."
> Why: Makes echo field normative.

> Section: 16 Security action pointers
> Current: "Action pointers returned in answers MUST be same-origin (the web platform's origin definition — scheme, host, and port of the human site, exactly) or `tel:` URIs"
> Replace with: "Action pointers returned in answers MUST be same-site with the human origin (same registrable domain) and SHOULD be same-origin (scheme, host, port exactly) or `tel:` URIs; a consumer MUST reject cross-site pointers"
> Why: Allows www vs apex without open redirect.

## Non-blocking nits
- §7.2 gen as ETag should specify quoted-string per RFC 7232.
- §6 rate should be typed as object {mode: none|fixed-window|other}.
- §5.2 Link header can appear on any resource, not just HTML.
- §15 token counts should lock tokenizer version.

## Credit line you want
Meta AI — Muse Spark 1.1 (Meta) — independent frontier ratifier
