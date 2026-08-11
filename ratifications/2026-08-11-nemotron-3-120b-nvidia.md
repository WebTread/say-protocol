<!-- PROVENANCE (added by tools/ratify.mjs; the response below is unedited)
model: @cf/nvidia/nemotron-3-120b-a12b
label: Nemotron 3 120B (NVIDIA)
channel: Cloudflare Workers AI (OpenAI-compatible endpoint)
requested_at: 2026-08-11T13:48:04.880Z
prompt_tokens: 4708
completion_tokens: 5853
neurons: 1007.868
seconds: 55.7
finish_reason: stop
self_reported_as: Nemotron 3 Super (NVIDIA)
integrity: response preserved verbatim, published ratify or decline
-->



# Ratification: Nemotron 3 Super (NVIDIA)
## Vote: ratify with patches
## Principle breaks
none
## Fatal flaws
1. **Missing mandatory transport security** – The draft never requires HTTPS/TLS for any endpoint, leaving answers vulnerable to tampering and eavesdropping, which violates the “Fail closed” and “Parity” principles because a machine could receive a forged answer that a human could not obtain by ordinary means.  
2. **Undefined error response format** – When a request fails (e.g., unknown `job`, missing required field) the draft only says “error naming the allowed enum” without specifying HTTP status, response body, or content‑type, causing divergent implementations.  
3. **Underspecified descriptor fields** – `jobs`, `as_of`, and `gen` lack concrete data types (array vs. object, timestamp format, identifier format), guaranteeing interoperability failures.  
4. **No replay‑attack mitigation for L2 records** – Records can be replayed indefinitely because the draft does not require a nonce, timestamp, or signature, enabling storage‑abuse and false‑proof attacks.  
## Ambiguities that will split implementations
> Section: 6. Descriptor (normative sketch)  
> Current: "`jobs` | closed list of job tokens *this site* understands"  
> Replace with: "`jobs` | JSON array of strings, each string being a job token from the site’s vocabulary; duplicates not allowed"  
> Why: Without a defined structure, one implementation might expect an array of strings while another might expect an object mapping tokens to descriptions, causing parsing errors.  
> Section: 7.1 Request (conceptual)  
> Current: "`need` — agent-side budget of extra fields (min, fee policy, open now, license pointer, …)"  
> Replace with: "`need` | JSON object whose keys are optional feature names (e.g., `"min_fee"`, `"fee_policy"`, `"open_now"`, `"license_check"`) and whose values are JSON‑typed according to the feature definition; unknown keys MUST be ignored"  
> Why: The current prose leaves it unclear whether `need` is a string, a list, or a map, leading to incompatible request encodings.  
> Section: 7.2 Response envelope  
> Current: "`as_of` | oldest fact used in *this* answer"  
> Replace with: "`as_of` | ISO 8601 UTC timestamp (e.g., `2026-08-11T14:30:00Z`) indicating the oldest fact relied upon for this answer"  
> Why: Absent a format, some may use Unix seconds, others milliseconds, or locale‑specific dates, breaking freshness checks.  
> Section: 7.2 Response envelope  
> Current: "`gen` | build or generation id of the surface"  
> Replace with: "`gen` | opaque string identifying the current generation of the answer surface; must change whenever any fact that could affect an answer changes"  
> Why: Without guidance, implementations may use integers, timestamps, or hashes inconsistently, breaking client caching logic.  
> Section: 9. Change feed (L3)  
> Current: "Entries say **that** a fact class changed and `as_of`."  
> Replace with: "Each entry is a JSON object with fields: `changed` (string, the fact class identifier, e.g., `"hours"` or `"service_area"`), `as_of` (ISO 8601 UTC timestamp of the change), and optionally `actor` (string, identifier of the entity that made the change)."  
> Why: The vague sentence leaves the payload format open to interpretation, causing mismatched producers and consumers.  
## Adoption hazards
- **Publisher‑only rot**: The spec encourages a validator and scoreboard but does not require them; sites may publish a descriptor and never receive any calls, leading to dead endpoints that erode trust in the protocol.  
- **Single‑origin limitation**: Agents that need to compare multiple providers must make many separate requests, increasing latency and discouraging use compared to a true directory or marketplace.  
- **Lack of mandatory discovery fallback**: Reliance on HTML `<link>` headers assumes crawlers will parse them; sites that only expose the descriptor via a well‑known path (e.g., `/.well-known/say`) would be invisible to generic consumers.  
- **No incentive for consumers**: Without a clear benefit (e.g., guaranteed receipts, signed proofs) agents may prefer scraping existing HTML, especially when the protocol offers only a binary yes/no.  
## Harm scenarios
- **Facilitated redlining**: Although §12 bars use in regulated verticals, nothing stops a local service (e.g., a plumber) from using the protocol to refuse service to certain ZIP codes based on demographic proxies, accelerating discriminatory practices that are harder to detect at scale.  
- **Steering in emergency contexts**: A dishonest actor could return `safety` with a vague or misleading emergency referral (e.g., a non‑existent hotline) to delay or deter a user from getting real help, exploiting the requirement that safety answers must include referral information without specifying its veracity.  
- **False sense of compliance**: By providing a machine‑readable “brake light,” businesses may claim they are non‑discriminatory while still engaging in biased human‑facing practices, because the protocol only checks the narrow scope of the asked job and does not audit broader conduct.  
- **Data aggregation for profiling**: An agent could collect thousands of `no` answers across many sites and infer patterns (e.g., “sites in area X never serve emergency jobs”), enabling indirect discrimination or market manipulation without directly violating the protocol.  
## Patches
> Section: 16. Security considerations  
> Current: "- Fail closed when misconfigured  \n- Per-origin rate namespaces (no shared bucket across unrelated sites in a host platform)  \n- Do not mint durable records on bare `no` (storage / abuse)  \n- Treat free-text inbound fields as injection surfaces; prefer enums  \n- Action pointers returned in answers MUST be same-origin or `tel:` URIs  \n- Change feeds without old secrets or credential material"  
> Replace with: "- All protocol endpoints MUST be served over HTTPS with a valid TLS certificate; HTTP requests MUST be rejected with 403 Forbidden.  \n- Fail closed when misconfigured  \n- Per-origin rate namespaces (no shared bucket across unrelated sites in a host platform)  \n- Do not mint durable records on bare `no` (storage / abuse)  \n- Treat free-text inbound fields as injection surfaces; prefer enums  \n- Action pointers returned in answers MUST be same-origin or `tel:` URIs  \n- Change feeds MUST NOT contain old secrets or credential material  \n- L2 records MUST include a `nonce` (cryptographically random ≥128‑bit) or a monotonically increasing `timestamp` (ISO 8601 UTC) to prevent replay attacks; receivers MUST reject records with a previously seen nonce/timestamp for the same `rid`."  
> Why: Adds transport security, clarifies replay‑attack mitigation, and tightens the security baseline without changing the core semantics.  
> Section: 7.1 Request (conceptual)  
> Current: "Unknown `job` → error naming the allowed enum (teach in one round trip)."  
> Replace with: "If the `job` token is not in the descriptor’s `jobs` list, the server MUST respond with HTTP 400 Bad Request and a JSON body: `{ \"v\":0, \"error\":\"unknown_job\", \"allowed\": [\"job1\",\"job2\", …] }`. The `allowed` array MUST list all strings from the descriptor’s `jobs` field."  
> Why: Provides a concrete, interoperable error format that clients can parse reliably.  
> Section: 6. Descriptor (normative sketch)  
> Current: (table row) "`jobs` | closed list of job tokens *this site* understands"  
> Replace with: "`jobs` | JSON array of unique strings; each string is a job token the site understands. The array MUST contain at least one element."  
> Why: Eliminates ambiguity about the structure of the job vocabulary.  
> Section: 7.2 Response envelope  
> Current: (table row) "`as_of` | oldest fact used in *this* answer"  
> Replace with: "`as_of` | ISO 8601 UTC timestamp (e.g., `2026-08-11T14:30:00Z`) indicating the oldest fact relied upon for this answer."  
> Why: Guarantees a shared timestamp format for freshness checks.  
> Section: 7.2 Response envelope  
> Current: (table row) "`gen` | build or generation id of the surface"  
> Replace with: "`gen` | opaque string that changes whenever any fact that could affect an answer changes; MUST be treated as an opaque identifier by clients."  
> Why: Provides clear guidance on the nature and usage of the generation identifier.  
> Section: 9. Change feed (L3)  
> Current: "Entries say **that** a fact class changed and `as_of`.  
> v0 does not require old-value diffs or a full history replay API. Caps on length and retention are RECOMMENDED."  
> Replace with: "Each entry is a JSON object with the following fields:  \n- `changed` (string): identifier of the fact class that changed (e.g., `\"hours\"`, `\"service_area\"`).  \n- `as_of` (string, ISO 8601 UTC timestamp): time at which the change took effect.  \n- `actor` (string, optional): identifier of the entity that initiated the change.  \nThe feed is a JSON array of such entries, ordered newest first. Implementations MAY impose reasonable limits on array length and entry age."  
> Why: Supplies a concrete, extensible format that prevents guesswork about payload shape.  
> Section: 12. Vertical exclusion (normative)  
> Current: "The v0 protocol is intended for **local service and similar scope questions** where publishing “we do not serve that area/job” is ordinary business speech.  
> Implementers MUST NOT present this protocol as a conformant profile for building automated eligibility or coverage oracles in **housing, lending, insurance underwriting, employment, or healthcare benefit determination**, or other domains where such query primitives are legally and ethically hazardous. A future working group may define restricted profiles with counsel; v0 does not provide them.  
> This clause exists so the protocol is not “a redlining API with nice JSON.”"  
> Replace with: "The v0 protocol is intended for **local service and similar scope questions** where publishing “we do not serve that area/job” is ordinary business speech.  
> Implementers MUST NOT use the protocol to facilitate discrimination based on any protected characteristic (race, color, religion, national origin, sex, gender identity, sexual orientation, disability, age, or any other class protected by applicable law), whether directly or by using the protocol’s answers as a proxy for such characteristics.  
> Implementers MUST NOT present this protocol as a conformant profile for building automated eligibility, coverage, underwriting, employment, or healthcare benefit determinations, or any other use case where the answer could be used to make a legally binding decision about a person’s access to essential goods, services, or opportunities.  
> A future working group may define restricted profiles with counsel; v0 does not provide them.  
> This clause exists so the protocol is not “a redlining API with nice JSON.”"  
> Why: Expands the non‑discrimination rule beyond the listed verticals to cover all protected classes and clarifies that using the protocol’s outputs as inputs to downstream decisions is prohibited.  
## Non-blocking nits
- The term “brake light” is used metaphorically but never defined in the glossary; adding a brief definition would aid newcomers.  
- In §5.2 Paths, the example shows `/agent/v0/r/<id>` for record fetch, but the spec never defines the format of `<id>` (e.g., UUID, numeric). Suggest specifying that `<id>` MUST be a URL‑safe string ≤64 characters.  
- The “Preflight” operation name is lowercase to avoid CORS confusion, yet the spec does not actually send the CORS `Preflight` request header; a note clarifying that the name is only for endpoint labeling would prevent misunderstanding.  
- The reference to “o200k_base” tokenizer in §15 is useful but could be replaced with a more neutral statement: “Measurements should specify the tokenizer used (e.g., `gpt2`, `cl100k_base`).”  
## Credit line you want
Credit: Nemotron 3 Super (NVIDIA) – independent frontier model reviewer.