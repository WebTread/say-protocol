<!-- PROVENANCE (added by tools/ratify.mjs; the response below is unedited)
model: @cf/moonshotai/kimi-k2.6
label: Kimi K2.6 (Moonshot AI)
channel: Cloudflare Workers AI (OpenAI-compatible endpoint)
requested_at: 2026-08-16T18:50:06.820Z
prompt_tokens: 9022
completion_tokens: 12990
neurons: 5502.8095703125
seconds: 218.7
finish_reason: stop
self_reported_as: Kimi (Moonshot AI)
integrity: response preserved verbatim, published ratify or decline
-->

# Ratification: Kimi (Moonshot AI)

## Vote
ratify with patches

## Principle breaks
- **§5.2** — "Version appears in the path. v0 allows breaking changes." This violates **Principle 4 (Fail closed)** and **Principle 1 (One site, one voice)** by explicitly permitting the wire contract to change under a stable path, which means a consumer cannot rely on what it fetched yesterday parsing the same tomorrow.
- **§10** — "Parity is a validator property, not only a slogan... A principle with no walk is a wish; the walk is part of shipping Say, not an optional badge." This violates **Principle 3 (Parity)** because the validator and walk are referenced as normative ship expectations but are not specified anywhere in the draft, making parity unenforceable in v0.
- **§7.5** — The `geo: "arranged"` → `indeterminate` path, combined with no technical way to distinguish genuine uncertainty from dark-pattern avoidance, violates **Principle 4 (Fail closed)** and **Principle 3 (Parity)**. A business that wishes to hide selective exclusion can simply declare every job `arranged`.
- **§12** — The vertical exclusion is a normative "MUST NOT present" but the protocol provides no technical guardrails (no schema restrictions, no mandatory validator flags, no discovery prohibition). This violates **Principle 7 (Non-harm)** by making harm structurally easy and enforcement structurally impossible.

## Fatal flaws
1. **Breaking changes permitted inside a versioned path (§5.2).** A standard that tells implementers "v0 allows breaking changes" is not a standard; it is an unstable API contract. Interoperability requires that a versioned path promise wire stability.
2. **Descriptor omits mandatory vocabulary declarations for `why`, `need`, and `where` shape (§6, §7.1).** The ask operation depends on site-declared vocabularies, but the descriptor sketch does not require the origin to publish the closed sets for `why` (refusals), `need`, or the valid values/shape for `where`. Two competent engineers will build incompatible consumers.
3. **Life-safety classes and vertical profiles are referenced but undefined in v0 (§7.6).** The draft says "defined per vertical profile" and gives examples (gas leak, CO, flooding), but v0 has no vertical profile mechanism and no closed list. Conformance is untestable.
4. **No HTTP status codes specified for ask responses.** The draft never states that a successful ask returns 200, or that errors use 4xx/5xx. Some implementations will return 204 for `no`, 200 for `yes`, or 404 for `indeterminate`, splitting the ecosystem immediately.
5. **No CORS requirements for web-facing endpoints (§16).** The protocol is designed for web agents, yet it omits any requirement for cross-origin headers. Browser-based agents and client-side validator walks will be blocked by default, forcing unnecessary proxy infrastructure.
6. **`valid_until` ABSENT rule creates a silent, undiagnosable failure mode (§6).** A small business that forgets to update a date field sees their entire surface vanish from agents with no human-visible symptom. The draft even says the origin SHOULD 404 itself. This is a foot-gun aimed at the least sophisticated operators.

## Ambiguities that will split implementations
> **§7.1** — "`where` — geography expressed at the site's declared grain (the param is grain-neutral by design: a region business answers regions, a zip business answers zips)"
- **Reading A:** `where` is a single string, e.g., `"where": "90210"`.
- **Reading B:** `where` is an object, e.g., `"where": {"zip": "90210"}`.
- **Reading C:** `where` is an array of regions, e.g., `"where": ["North Bay", "South Bay"]`.
- **Result:** Consumers will guess, and origins will reject validly formed requests because the spec never pins the JSON type.

> **§7.2** — "`as_of` — oldest fact used in *this* answer, as an RFC 3339 date (`YYYY-MM-DD`) or full UTC date-time, at the precision the site actually tracks and never finer"
- **Reading A:** The field is always a full-date string.
- **Reading B:** The field may be a full-date or a date-time, chosen by the origin.
- **Result:** Consumers must write two parsers and tolerate heterogeneity, or fail to parse half the ecosystem.

> **§7.1** — "The descriptor declares the error identifiers an origin can emit, exactly as it declares every other vocabulary."
- **Reading A:** Error `type` values are simple strings like `unknown_job`.
- **Reading B:** Error `type` values are absolute URIs per RFC 9457, prefixed with the origin.
- **Result:** RFC 9457 expects absolute or relative URIs; the draft says "origin-relative error identifier." Implementers will disagree on whether to resolve against the descriptor URL or treat it as an opaque token.

> **§7.1** — "`agent.purpose` — `prequalify` | `compare` | `book_intent` | `research` | `audit`"
- The draft does not state whether this field is required or what the default is when omitted.
- **Result:** Some origins will 400 on missing `agent.purpose`; others will ignore it; others will default to `research`. Divergent strictness.

> **§6** — "`rate` — the implementation's DECLARED rate posture, including honestly declaring `"none"`"
- No type is given. Is it a string? An object with `limit` and `window`? An array of tier objects?
- **Result:** Every implementer will invent their own shape.

## Adoption hazards
- **Publisher-only rot.** §1 correctly diagnoses the `llms.txt` empty-file problem and promises a consumer path, validator, and scoreboard. None are specified in the draft. Without a concrete validator specification and at least one maintained open-source consumer, this becomes another badge file that SEO consultants sell and agents ignore.
- **Shared-hosting exclusion.** §5.1 requires HTTP `Link` headers and §16 requires HTTPS. Many small local businesses sit on cheap shared hosts that do not allow custom headers and charge extra for TLS. The conventional path fallback helps, but the header requirement still splits the ecosystem into "sites that can afford custom headers" and "sites that cannot."
- **`valid_until` footgun.** A protocol that 404s itself because a calendar field expired will punish the very small businesses with no devops team that this protocol claims to serve. They will not notice they are invisible to agents until they lose jobs.
- **Competitor reluctance / branding capture.** Despite the strong non-affiliation clause, the name "Say" is phonetically and orthographically close to "SayVel," and the first live implementation, reference stack, and plugin roadmap all originate from SayVel. Competitors may treat this as a vendor capture attempt and refuse to implement, regardless of the masthead disclaimer.
- **Token-count myopia.** §15 publishes token budgets using a specific proprietary tokenizer (`o200k_base`). This signals that the protocol is optimized for one model ecosystem, which may deter agents built on other tokenizers or non-LLM consumers.

## Harm scenarios
- **Redlining by proxy, at scale.** A business selects zip codes or region names that correlate with race or income, emits `no` with `basis: crew_reach`, and an aggregator queries thousands of Say surfaces to build a coverage map. §12 calls this a prohibited use, but the data is public and the API is unauthenticated. You cannot normatively prohibit someone from calling a public endpoint and writing down the answers. The protocol makes discriminatory coverage machine-readable and machine-mappable.
- **Housing discrimination laundered through home services.** Plumbing, HVAC, electrical, and pest control are all "local service" jobs that landlords use to maintain properties. A landlord or property management platform could adopt Say to answer repair requests. By declaring `job_not_served` or `out_of_area` for units in Section 8 or rent-stabilized buildings while answering `yes` for market-rate units in the same zip, they create a discriminatory filter that §12 nominally forbids but does not technically prevent.
- **Emergency referral errors and liability.** §7.6 forces origins to refer to public emergency services for life-safety classes. For a 24/7 locksmith, a child-locked-in-car scenario is a legitimate emergency they should respond to; forcing a 911 referral may delay rescue. Conversely, if a national chain receives a `where` value it cannot map to a jurisdiction, the protocol still demands a safety referral. The origin may guess wrong, publishing an incorrect emergency number for a region it does not know, creating liability for wrong referrals.
- **Accidental contract formation.** If a consumer sends `need: ["fee_policy"]` and the origin returns a price in a `yes` payload, a jurisdiction could treat that structured response as a firm offer or quote, especially in home-service contexts. The draft warns against firm offers in descriptors but not in `yes` answers.
- **PII retention in L2 records.** An agent may include a street address in `where` (even though the spec discourages fine geo). The origin mints a record with that address. §17 says records "should avoid" storing personal data. A standard must not standardize PII retention with "should avoid."
- **Stalking and operational security.** A domestic abuser can poll `open_now` + coarse geo for a shelter or reproductive health clinic that implements Say, using the structured response to infer hours and service availability without ever visiting the human site.

## Patches

> Section: 5.2 Paths
> Current: "Version appears in the path. v0 allows breaking changes."
> Replace with: "Version appears in the path. Once a versioned path is advertised publicly, deployments MUST NOT introduce breaking wire changes to that path. Breaking changes require a new version segment (e.g., `/say/v1/`). Until the publication gates in §19 clear, this draft may still change, but frozen v0 implementations must remain parseable by v0 consumers."
> Why: A standard cannot permit breaking changes within a single versioned path and expect interoperability.

> Section: 6 Descriptor (normative sketch)
> Current: "JSON object including at minimum:" [table]
> Replace with: "The descriptor MUST be a valid JSON object. Required top-level fields are `v`, `as_of`, `jobs`, `grain`, `endpoints`, `auth`, `valid_until`. The descriptor MUST declare the site's closed `why` vocabulary in a `refusals` array of strings and its closed `need` vocabulary in a `needs` array of strings. The `jobs` field MUST be a JSON array of objects; each object MUST contain a string `id` unique within the array and MAY contain `geo` with value `\"arranged\"` or a string matching the site's declared `grain`."
> Why: Without mandatory vocabulary declarations for `why` and `need`, consumers cannot interpret refusals or request lean fields, and implementations will diverge.

> Section: 7.1 Request (conceptual)
> Current: "`where` — geography expressed at the site's declared grain (the param is grain-neutral by design: a region business answers regions, a zip business answers zips)"
> Replace with: "`where` — a string or ordered array of strings expressing geography at the site's declared grain. The descriptor MUST declare the JSON Schema type for `where` in a `where_schema` field (e.g., `{\"type\":\"string\"}` or `{\"type\":\"array\",\"items\":{\"type\":\"string\"}}`). If the grain vocabulary is a closed set, the descriptor MUST list the valid values in `grain_values`."
> Why: Consumers cannot construct valid geographic queries without knowing the expected data type and valid vocabulary.

> Section: 7.2 Response envelope
> Current: "Every answer includes:" [table]
> Replace with: "A successful ask operation MUST return HTTP 200 OK. The response body is a JSON object including:"
> Why: Without a pinned success status, implementations will diverge (e.g., 204 for `no`), breaking generic HTTP clients.

> Section: 7.6 safety
> Current: "Life-safety classes (gas leak, CO, active flooding, etc., defined per vertical profile) MUST NOT return a bare `no` without emergency referral information."
> Replace with: "For any `job` entry in the descriptor that includes `life_safety: true`, the origin MUST NOT return a bare `no` without emergency referral information. v0 defines no closed list of life-safety classes; vertical profiles after v0 MAY add them. An origin MUST NOT mark a job as `life_safety: true` unless it has set `safety.human_reviewed` to `true`."
> Why: v0 references undefined profiles and classes, making the safety clause untestable and unenforceable.

> Section: 7.6 safety
> Current: "when the ask omits geography, the origin defaults to its own primary service jurisdiction and marks the answer `jurisdiction: \"assumed\"` — a conformant safety answer is therefore always possible (this closes an impossible-conformance defect caught in ratification round 1)."
> Replace with: "When the ask omits geography, the origin defaults to its own primary service jurisdiction and marks the answer `jurisdiction: \"assumed\"`. When the ask includes geography the origin cannot map to a jurisdiction, the origin MUST answer `indeterminate` with `why: jurisdiction_unknown` and MUST NOT invent referral information."
> Why: The protocol currently forces a safety referral even when the origin lacks jurisdiction knowledge, creating liability for wrong emergency numbers.

> Section: 6 Descriptor / 16 Security considerations
> Current: "All protocol endpoints MUST be served over HTTPS."
> Replace with: "All protocol endpoints MUST be served over HTTPS. Endpoints MUST include CORS headers allowing cross-origin GET and POST requests (`Access-Control-Allow-Origin: *` at minimum for anonymous L1 asks) so that browser-based agents and validator walks can execute without proxy infrastructure."
> Why: A web protocol consumed by agents that omits CORS assumes all callers are server-side, which excludes a huge class of browser-based and plugin consumers.

> Section: 6 Descriptor
> Current: "`valid_until` — a date after which consumers MUST treat this surface's artifacts as ABSENT, not merely stale — and past which a live origin SHOULD go dark server-side too (the reference implementation 404s itself on expiry; an abandoned install must not serve dead facts forever)"
> Replace with: "`valid_until` — an RFC 3339 full-date after which consumers SHOULD treat cached answers as stale and MUST revalidate via the descriptor before relying on them. The maximum permitted value is 90 days after `as_of`. Origins SHOULD return 410 Gone for the descriptor past this date, but MUST NOT return 404 for other endpoints without an explicit `surface_disabled` refusal."
> Why: Forcing ABSENT creates a reliability foot-gun for small businesses; a maximum bound prevents abandoned long-dated descriptors.

> Section: 12 Vertical exclusion (normative)
> Current: "This clause exists so the protocol is not 'a redlining API with nice JSON.'"
> Replace with: "This clause exists so the protocol is not 'a redlining API with nice JSON.' Because the protocol's answers are public, aggregators may still build coverage maps. Therefore, validators MUST perform a coverage-pattern test comparing an origin's geographic yes/no distribution against public demographic data for its declared jurisdiction. Origins whose coverage correlates with protected-class geography beyond operational justification MUST be flagged non-conformant."
> Why: A normative prohibition on public data use is unenforceable; the only practical defense is validator-side statistical testing.

> Section: 17 Privacy considerations
> Current: "Records should avoid storing principal personal data unless required for the business transaction path defined outside this protocol"
> Replace with: "Records MUST NOT store the `where` value or any other ask parameter beyond `job`, `urgency`, and `need` keys. Personal data required for a transaction MUST be handled outside the Say protocol surface."
> Why: A standard for machine-readable business scope must not become a standardized PII retention endpoint.

## Non-blocking nits
- **§15** — Publishing token counts tied to `o200k_base` binds an open standard to a specific proprietary tokenizer. Replace with a neutral measurement or move to implementation notes.
- **§20b** — The "ARGUE B1–B7" drafting-room commentary should be removed before publication; it belongs in commit messages, not a normative document.
- **§22** — The signatures table includes "Ratifying models" with status "Round opens after Beacon countersign." This is meta-process commentary that should be removed from the published standard.
- **§4** — "Ask" appears twice in the terminology block (once as noun, once as operation), which is confusing. Distinguish them or merge.
- **§7.4** — If `need` includes price-related fields, add a mandatory disclaimer that the `yes` response is not a firm offer, to reduce contract-formation risk.

## Credit line you want
Kimi (Moonshot AI)