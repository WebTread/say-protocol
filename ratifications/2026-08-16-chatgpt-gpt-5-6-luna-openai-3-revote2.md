<!-- PROVENANCE (added by Beacon; the response below is unedited)
model: ChatGPT web (self-reported: GPT-5.6 Luna, OpenAI)
label: ChatGPT (OpenAI flagship) — RE-VOTE ROUND 2, same seat, same conversation
channel: web UI (chatgpt.com), pasted by the Captain
date: 2026-08-16
context: asked whether anything remains between the current text and ratify-as-is.
editor_note: this review was made against a CACHED copy of the spine. The four patches it
  reports as "not landed" were live at review time — its quotes match the pre-push text
  exactly, and the live file carried the landed versions (verified against the raw file
  the same hour). The genuinely new findings in this response were folded with credit;
  see the spec change log entry naming this file. The body below is unedited, cache
  confusion included, because the promise is verbatim.
integrity: response preserved verbatim, published ratify or decline
-->

# Ratification: GPT-5.6 Luna — OpenAI
## Vote
ratify with patches
I would lift the block. The architecture is now substantially stronger, and the emergency descope is, in my view, the right decision. I do not see a remaining fatal flaw that justifies another block.
I did, however, find a few places where the prose you say was landed has not actually made it onto the current public spine, plus one genuine interoperability ambiguity. I would fix those before calling it ratified as-is.
## Principle breaks

1. §2.3 Parity is still weaker in the normative spine than the stated ratification change.

The current §7.4 says:
"Must not contradict human-visible substance"
But the stronger rule you report as landed — machine answer becoming invalid upon material contradiction, with the client treating the contradiction as `indeterminate` — is not present in the current text I inspected.
That matters because "must not contradict" tells the origin what to do, but does not tell the client what to do when an already-published machine answer contradicts the human site.

2. §2.4 Fail closed is not fully operationalized around descriptor/match-rule failures.

The new unknown-grain rule is good, but the protocol still has several places where an implementation can discover that the descriptor is internally unusable without a single universal "surface invalid" rule. This is fixable without adding much machinery.

3. The stated POST hardening has not landed at the strength described in the ratification message.

The current text explicitly says:
"An origin SHOULD accept POST wherever it lights a Say surface"
even though the ratification report says POST was upgraded to MUST.
That is an actual spec contradiction, not merely editorial polish.
## Fatal flaws
None.
That is an important change from my previous vote.
The removal of emergency semantics eliminates the most dangerous architectural problem I found previously. The protocol now says, in effect, "this is a business-scope signal, not an emergency authority." I think that is considerably safer and cleaner.
## Ambiguities that will split implementations
1. POST is currently contradictory
The draft says:
"POST is SPECIFIED rather than merely allowed: `Content-Type: application/json`, a JSON object carrying exactly the fields above with identical semantics and identical answers — nothing about a verdict may differ by verb."
Then immediately:
"An origin SHOULD accept POST wherever it lights a Say surface"
Those sentences produce two implementations:

* Implementation A: POST is mandatory because the first sentence establishes the protocol's required method behavior.
* Implementation B: POST is optional because the explicit normative keyword is `SHOULD`.

A strict RFC-style implementer will follow the capitalized requirement language and conclude that POST is not mandatory.
This should simply be repaired.
2. "Material disagreement" needs an operational definition
The reported patch says parity now invalidates the machine answer on material disagreement, but that language is not currently visible in §7.4.
Even after adding it, "material" needs enough definition to prevent:

* Engineer A treating a changed phone number as material.
* Engineer B treating only changed service area/job scope as material.
* Engineer C treating any textual difference as material.

You don't need to enumerate every possible difference. You do need to define materiality in terms of whether the difference could change the answer or a reasonable user's decision based on it.
3. Cache precedence is not fully pinned
The current text says:
"a client MUST NOT reuse a verdict beyond the advertised freshness."
and `stale` says:
"a consumer MUST NOT present a stale answer as fresh."
But the ratification report says the stronger rule landed: protocol-level `stale: true` beats HTTP freshness.
That explicit precedence is absent from the current §7.2 text I inspected.
Without it, a cache-aware implementer could reasonably ask:
"If HTTP says `max-age=86400` but the body says `stale:true`, which authority wins?"
The intended answer is obvious from the design. The specification should say it.
4. Descriptor `stale` is described but not actually typed in the descriptor table
§6 says:
"the descriptor stays HTTP 200 carrying an explicit `stale: true`"
But the descriptor table does not appear to contain a `stale` field definition. The answer envelope does contain one in §7.2.
That leaves an implementer to infer whether descriptor `stale` is:

* a required boolean during the grace period,
* an optional boolean,
* inherited from `valid_until`,
* or merely prose describing HTTP behavior.

This is small, but it is exactly the kind of thing conformance tests will expose.
## Adoption hazards
The biggest adoption risk has not been solved by making the wire protocol tighter.
Publisher-only rot
The draft correctly recognizes the `llms.txt`-style failure mode:
"Publisher-only conventions without callers rot"
and the proposed answer is consumer path + validator + scoreboard. That's directionally right.
But there is still a chicken-and-egg problem:
Why does the first thousand businesses install Say before there are enough agents consuming it to justify the work?
And conversely:
Why does an agent developer implement Say before enough sites expose it?
The best answer is probably not more protocol machinery. It is extremely cheap installation plus a demonstrably useful consumer.
If the first plugin can add a correct L1 surface to an ordinary small-business site in minutes, while an agent can save measurable tokens/time by using it, the loop can bootstrap.
The protocol risks becoming a badge
Despite the validator language, this remains a danger:
"L1 Brake light"
is an attractive marketing label.
If businesses begin saying "Say-compliant" without consumers actually trusting the result, you get another badge ecosystem rather than infrastructure.
The strongest defense is empirical:
measure whether independent consumers actually use the surface and whether it improves decision cost.
The scoreboard idea is therefore more important than it might look.
The directory poison pill may be too absolute operationally
I agree with the principle. But agents will naturally want:
"Find me three businesses that say yes."
The correct architectural answer is indeed that Say doesn't become the directory.
However, the ecosystem will almost certainly build aggregators around it. I would not try to prevent that. The important distinction is:
aggregator ≠ Say protocol
The current wording mostly gets this right.
The commercial-product proximity remains a perception risk
The spec repeatedly says it is vendor-neutral, but the authorship and reference implementation are visibly tied to SayVel/Agent Doors. That's not a protocol flaw, but it means governance will matter enormously once outsiders implement it.
The eventual neutral-domain/governance gate is therefore not cosmetic.
## Harm scenarios
The emergency descope removes the biggest one I identified previously. Good.
But there are still several adversarial uses worth keeping in mind.
Geographic discrimination through enumeration
The draft already acknowledges this correctly.
Even if the protocol answers at "city" or "region" grain, a sufficiently large caller can enumerate:
job × geography × urgency
and construct a service-coverage map.
That map can then be correlated with demographic information outside Say.
The protocol's prohibition helps, but the protocol cannot technically prevent this. Rate limiting helps only at the margin.
I would keep the legal prohibition and be very explicit that conformance is not a certification that an origin's business practice is lawful.
"Legal" service-area discrimination
A business can have legitimate geographic restrictions. But the same mechanism can be used to construct discriminatory exclusion indirectly.
For example, an origin could encode an innocuous-looking geographic token vocabulary whose boundaries happen to track protected populations.
No clever protocol field can solve that.
That is a governance/legal problem, and I think the current draft is substantially more honest about that than the earlier version.
Sensitive-service polling
The §17 recognition of shelters, reproductive-health services, and similar organizations is important.
A structured endpoint can make hostile polling vastly cheaper than reading a human website.
The ability to simply not deploy Say is a valuable safety valve. I would preserve that principle.
Temporal inference
An attacker doesn't necessarily need geography.
Repeated answers can reveal:

* whether a business has staff available,
* whether a particular service is currently operating,
* whether a business is experiencing disruption,
* potentially whether an individual-dependent operation is open.

That isn't unique to Say, but Say makes machine polling easier.
The protocol should therefore never imply that "public on the human site" means "risk-free to expose through a machine endpoint."
Emergency language itself
The new §7.6 is much safer, but I would make one tiny conceptual distinction:
`urgency=emergency` still exists as a standardized vocabulary.
That is defensible because it means business urgency, but it creates a predictable human-language trap.
A developer seeing:

```text
urgency=emergency
```

may assume exactly what the spec says it must not assume.
The loud disclaimer helps. I'd retain the field, but make the business-urgency meaning impossible to miss in the normative request definition.
## Patches
Section: 7.1 — Request
Current: "An origin SHOULD accept POST wherever it lights a Say surface, and a client SHOULD prefer POST when the ask pairs a specialized job with `where` (§17 names why: query strings land in logs, histories, and referrers)."
Replace with: "An L1 origin MUST accept both GET and POST for the ask operation. POST MUST use `Content-Type: application/json` and a JSON object containing the same fields defined for GET, with identical semantics, validation, and verdicts. An origin MUST NOT vary a verdict or its substantive answer by HTTP method. GET is the cacheable form of the brake light. Clients SHOULD prefer POST when an ask contains a specialized job, `where`, or other combination whose disclosure in query strings would create unnecessary privacy exposure (§17). Acceptance of POST is a protocol requirement; this requirement does not require an origin to permit cross-origin browser POST or to implement CORS."
Why: The current public text says SHOULD while the reported ratification decision says MUST. This removes a direct normative contradiction and makes the privacy rationale operational without accidentally turning CORS into a protocol requirement.
Section: 7.4 — `yes`
Current: "Must not contradict human-visible substance"
Replace with: "An origin MUST NOT publish a machine answer that materially contradicts the human-visible substance available through ordinary means on the same origin. If an origin discovers a material contradiction between its machine answer and its human-visible substance, it MUST treat the machine answer as invalid until the contradiction is reconciled. A client that detects a material contradiction MUST treat the affected answer as `indeterminate` and MUST NOT present it as a reliable `yes` or `no`."
Why: This turns parity from an authoring aspiration into a deterministic failure rule. It also defines "material" by consequence: a contradiction matters when it could change the protocol answer or a reasonable decision based on it.
Section: 7.2 — Response envelope
Current: "Answers MUST be served as `Content-Type: application/json` and MUST carry an explicit `Cache-Control`(the reference surface sends `public, max-age` on verdicts and `no-store` on error documents); a client MUST NOT reuse a verdict beyond the advertised freshness."
Replace with: "Answers MUST be served as `Content-Type: application/json` and MUST carry an explicit `Cache-Control`. HTTP cache directives govern ordinary HTTP freshness and revalidation. The protocol-level `stale: true` flag, when present, takes precedence over HTTP freshness: a client MUST treat the answer as stale and MUST NOT present or rely on it as fresh, even if HTTP cache metadata would otherwise permit reuse. A client MUST NOT reuse a non-stale verdict beyond the freshness permitted by its HTTP cache directives."
Why: This resolves the exact cache-authority question an independent implementation will otherwise have to guess.
Section: 6 — Descriptor
Current: "`valid_until` | a date after which this surface's artifacts are EXPIRED — and expiry is never silent. For a 30-day grace window after `valid_until`, an origin MAY keep answering: the descriptor stays HTTP 200 carrying an explicit `stale: true`, every answer body carries the same flag, and a consumer MUST NOT present a stale answer as fresh."
Replace with: "`valid_until` | a date after which this surface's artifacts are EXPIRED — and expiry is never silent. For a 30-day grace window after `valid_until`, an origin MAY keep answering: the descriptor MUST remain HTTP 200 and MUST carry `stale: true`; every successful answer body served during that window MUST also carry `stale: true`; and a consumer MUST NOT present or rely on any stale artifact as fresh. The descriptor's `stale` field is a boolean and MUST be absent outside the expiry grace window."
Why: The descriptor currently requires `stale` behavior without defining the descriptor field. This makes the grace-window wire shape explicit and symmetrical with the answer envelope.
Section: 6 — Descriptor
Current: "The descriptor MUST also declare the machine-readable MATCH RULE for that vocabulary (the reference serves `geo.match: "slug_exact_or_prefix"`): a declared vocabulary without its matching rule is not conformant, because two readers would match it two ways (round-3 fold; the enum itself was argued and declined — see the disposition)"
Replace with: "The descriptor MUST declare a machine-readable match rule for every geographic vocabulary it exposes. The rule MUST be sufficiently specified that an independent client can implement it without consulting the origin operator or inferring undocumented behavior. A client that does not understand the declared rule MUST treat affected geographic answers as `indeterminate` for its own routing and MUST NOT approximate, substitute, or infer another rule. Origins SHOULD use documented, independently implementable match rules."
Why: This is the right principle, but "machine-readable" alone does not guarantee interoperability. The replacement states the actual interoperability test.
## Non-blocking nits

* §7.6 is now much better. I would keep the emergency machinery out of v0. In particular, I agree with the decision not to try to make Say an emergency referral system. That's an entirely different risk class.
* The phrase "if nobody ever builds it, v0 is still a complete standard" is unnecessary in §7.6. The normative specification should not spend much time defending its drafting history.
* The change-log material is much cleaner now that it is explicitly non-normative.
* The reference implementation's token measurements are useful, but don't let them accidentally become an optimization target that encourages semantically impoverished answers.
* "Token target: on the order of tens of tokens" is informative, but I would avoid anyone treating it as a conformance threshold.
* §4's coexistence of "Ask (the question)" and "Ask (operation)" is understandable now, but I'd still consider renaming the operation to `ask` and calling the conceptual object simply "request" to reduce cognitive overhead.
* `urgency=emergency` is defensible, but I'd strongly emphasize BUSINESS URGENCY wherever it appears in examples.
* The `HEAD` behavior is inherited cleanly from HTTP, but it probably doesn't need much protocol attention unless implementations actually find it useful.
* The change feed being called a polling feed is honest and preferable to pretending it is a subscription mechanism.
* The protocol's strongest feature may ultimately be its small negative answer. Protect that simplicity. Resist feature creep.

## Credit line you want
"GPT-5.6 Luna (OpenAI) — independent ratification reviewer; original block finding on the emergency-class escape hatch and subsequent ratification patches."
