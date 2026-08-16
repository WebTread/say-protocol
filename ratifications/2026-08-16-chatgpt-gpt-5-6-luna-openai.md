<!-- PROVENANCE (added by Beacon; the response below is unedited)
model: ChatGPT web (self-reported: GPT-5.6 Luna, OpenAI)
label: ChatGPT (OpenAI flagship)
channel: web UI (chatgpt.com), pasted by the Captain
date: 2026-08-16
integrity: response preserved verbatim, published ratify or decline
-->

# Ratification: GPT-5.6 Luna — OpenAI

## Vote

block

## Principle breaks

1. **§2.3 Parity is not actually enforceable enough to support the protocol's central promise.**
   The draft says:

   > “Nothing is asserted to a machine that a human could not obtain by asking the business through ordinary means.”

   But §10 reduces parity to a validator walk over sampled asks:

   > “Conformance walks … MUST compare sample asks to human-visible substance on the same origin and fail cloaking or machine-only fiction.”

   A sampled walk cannot establish that *all* machine answers remain parity-safe. More importantly, “could obtain by asking,” “ordinary means,” and “human-visible substance” have no operational definition. Two validators can therefore disagree about whether a site conforms while both follow the text.

2. **§2.7 Non-harm conflicts with §6/§7's deliberately site-defined vocabularies.**
   The protocol gives the origin control over `jobs`, `grain`, `why`, `need`, and especially `safety.classes`, while simultaneously making broad non-discrimination claims. That is a dangerous combination: the protocol standardizes the *mechanism for answering*, but leaves much of the semantic policy being answered to the publisher.

3. **The “one site, one voice” principle is undermined by the absence of an authoritative definition of “one business.”**
   §13 permits:

   > “Operating many single-site surfaces is fine.”

   Yet nothing prevents a franchisor, property owner, platform, insurer, lead generator, or other entity from presenting a large collection of nominally independent origins. The protocol technically remains one-origin-at-a-time while functionally becoming the distributed oracle it says it is not.

4. **§2.4 “Fail closed” is incomplete.**
   The protocol says an unconfigured surface should fail cleanly, but it does not define what a client should do with many important *configured but unreliable* states: stale answers, contradictory human pages, redirects, partial outages, malformed descriptors, inconsistent `gen`, DNS/CDN errors, or a descriptor that says one endpoint exists but the endpoint returns another protocol version. “Absent” and “indeterminate” are not interchangeable in a decision system.

## Fatal flaws

### 1. The safety mechanism permits an origin to define away safety classes

This is the most serious flaw.

§7.6 says:

> “Life-safety classes … are DECLARED BY THE SITE in its descriptor's `safety.classes` … v0 mints no global list…”

and then:

> “An ask matching a declared life-safety class answers with the verdict `safety`, never `no`…”

This only protects a caller **after the origin has declared the hazard**. A business can simply omit `gas_leak`, `CO`, flooding, electrical fire, poisoning, or another dangerous class from `safety.classes`. There is then no protocol-level way for a client to know whether the ordinary `job`/`no` machinery is being applied to a life-safety situation.

That is particularly dangerous because the draft advertises `emergency` as globally legible:

> “urgency is the ASKER's word and MUST read the same at every origin, because §7.6 depends on `emergency` being legible everywhere.”

But `emergency` does not force `safety`. The site-controlled class vocabulary still decides whether the emergency is recognized as such.

This is a **semantic safety hole, not merely an implementation ambiguity**. A client can receive a perfectly conformant-looking `no` for an emergency because the publisher never declared that emergency class.

### 2. The protocol has no interoperable semantics for geography

The central answer is supposed to be “can you take this job here?”, but the most important part of that question is left site-defined:

> “`grain` | the site's OWN declared geographic decision vocabulary…”

and:

> “the reference descriptor carries both in its `geo` block (`geo.grain`, `geo.match: "slug_exact_or_prefix"`)”

The reference implementation's particular `slug_exact_or_prefix` rule is not actually standardized by the draft. A conforming implementation can choose a completely different vocabulary and matching semantics.

That creates incompatible answers from two otherwise conformant sites. One may interpret `where=phoenix` as city equality; another may interpret it as a service-region prefix; another may accept arbitrary human-entered strings. A caller cannot reliably normalize, compare, cache, or reason over answers.

The grain-lock prevents one kind of precision abuse, but it does not provide interoperable geographic semantics.

### 3. “No discrimination” is asserted as a normative requirement without a conformance mechanism

§12 says:

> “Implementers MUST NOT use this protocol to discriminate, directly or by proxy…”

That is a serious policy goal, but the standard supplies no testable definition of what constitutes prohibited proxy discrimination.

Worse, §10 explicitly says:

> “a tool MUST NOT label an origin discriminatory on pattern alone — that is counsel's ground…”

So the validator cannot establish the requirement, and the requirement cannot be reliably tested from the protocol. This leaves “conformance” dependent on an undefined legal judgment while simultaneously allowing highly enumerable geographic answers.

The result is a standard whose strongest safety promise is not mechanically enforceable, while its core primitive makes systematic enumeration unusually cheap.

### 4. The GET design creates a privacy and operational-security hazard

§7.2 makes GET the primary ask:

> “A GET carries the answer as a JSON body…”

while §7.1 allows `where`, `urgency`, `need`, and other parameters.

The draft never specifies whether those parameters belong in the query string, nor does it specify cache-control, `Vary`, referrer handling, logging requirements, or intermediary behavior.

If implemented conventionally as a query string, sensitive queries can appear in browser history, reverse-proxy logs, analytics systems, CDN logs, observability systems, and referrer metadata. Even coarse geography plus a specialized service can reveal sensitive circumstances.

This is particularly inconsistent with §17's privacy goals.

### 5. The “standard” is not sufficiently specified to permit independent conformance

The draft repeatedly refers to:

> “the public validator”

and:

> “Water Heater Walk”

but does not include a normative conformance test suite, schema, canonical examples, or sufficiently complete wire specification.

For example, there is no complete normative definition of:

* JSON schemas for descriptor/request/each response type;
* query parameter encoding;
* POST request format;
* required response media types;
* cache semantics;
* redirects;
* localization;
* URL resolution;
* `ETag`/`gen` interaction;
* stale-answer representation;
* exact `safety` response schema;
* exact `record` schema;
* exact `change` schema;
* `geo` schema;
* whether unknown JSON fields are ignored or rejected;
* whether duplicate parameters are legal;
* maximum request sizes.

A validator can choose answers to these questions, but then the **validator becomes the real standard**. That is backwards for a vendor-neutral protocol.

### 6. The RFC 9457 treatment is internally misleading

The draft says:

> “the wire `type` carries that identifier under the origin's error path, so a client matches on the identifier rather than string-comparing a descriptor entry against `type`.”

RFC 9457 does permit URI references, including relative references, but it defines the resolved URI as the problem type's primary identifier and notes that relative references can create different identities depending on the document base. ([RFC Editor][1])

The draft's bespoke “bare identifier in descriptor / origin-relative URI on wire / compare the identifier rather than the URI” layer therefore creates another mapping clients must implement. That mapping should either be standardized precisely or eliminated. Otherwise independent clients can disagree about normalization and comparison.

### 7. L2 “consequential yes” is undefined, despite records being the protocol's strongest evidentiary feature

§8 says:

> “A record represents checks performed and answer identity.”

and §3 says:

> “Consequential yes paths can mint an idempotent record…”

But “consequential” is never defined. One implementer may mint records only for booking; another may do it for every `yes`; another may treat a price request as consequential; another may not.

That matters because records have durability, privacy, and potential legal significance. This is not harmless optional metadata.

## Ambiguities that will split implementations

1. **Descriptor freshness**

   > “`as_of` | date of oldest material fact the surface will rely on”

   One implementation can set this to the oldest underlying business fact; another can set it to the oldest fact relevant to the requested answer. The response version uses “oldest fact used in *this* answer,” which is a different semantic rule. Clients cannot compare descriptor `as_of` and answer `as_of` consistently.

2. **`valid_until` versus stale state**

   > “For a 30-day grace window after `valid_until`, an origin MAY keep answering…”

   Does `valid_until` expire the descriptor's *facts*, the entire service description, or only the publisher's freshness guarantee? The answer body is required to contain `stale: true`, but `stale` is not included in the normative response schema in §7.2.

3. **POST is mentioned but unspecified**

   > “`/say/v0/ask` ask (GET primary; POST allowed)”

   What is the POST media type? JSON body? Form encoding? Are query parameters also permitted? Does POST have exactly the same semantics? Can it avoid GET privacy problems? Two competent implementations will differ.

4. **Unknown `need` members**

   > “unknown members are ignored and named back in the response”

   Is that still HTTP 200? Where are they named? Are they an error if *all* requested members are unknown? May the server return a partial answer? There is no response field defined for them.

5. **`grain` semantics**

   > “the site's OWN declared geographic decision vocabulary”

   Does a `grain` value describe a noun (“city”), a set of values (“Phoenix, Mesa”), a matching algorithm, or both? §6 and §7.1 distribute the semantics between `grain`, per-job `geo`, and `geo.match`, but do not define their precedence.

6. **“same origin”**

   > “Action pointers returned in answers MUST be same-origin or `tel:` URIs”

   Is `https://www.example.com` same-origin with `https://example.com`? What about a different port, subdomain, or internationalized hostname? This needs the web platform's exact origin definition, not colloquial “same-origin.”

7. **Record retrieval authentication**

   §8 discusses anonymous L1 and keyed identities, but does not say whether `/say/v0/r/<id>` is publicly readable. If it is public, the record can become an information-disclosure mechanism; if private, the authentication protocol is unspecified.

8. **Change-feed subscription semantics**

   §9 calls it:

   > “the subscription path”

   but defines only a JSON array. There is no polling cursor, conditional request contract, pagination, event identifier, or retention guarantee. That is a feed, not a subscription protocol.

9. **Business identity**

   `identity` is required at L0, but there is no rule for what constitutes the business represented by an origin. A franchise location, corporate site, individual practitioner, marketplace storefront, and call-center landing page can all implement the same shape.

10. **“ordinary means” parity**

    A business could answer a human by phone with information that is not on the site, while another only answers through a web form. Both could claim parity. The phrase is not sufficiently deterministic for a standard.

## Adoption hazards

### Publisher-only rot

The draft correctly identifies the `llms.txt`-style failure mode, but its solution still has a publisher-side adoption problem.

A business must maintain:

* a descriptor;
* job vocabulary;
* geography vocabulary;
* matching semantics;
* safety classes;
* freshness;
* change feed;
* optional records;
* complaint handling;
* HTTPS;
* validator conformance;
* parity with the human site.

That is substantial operational work for a feature whose direct value depends on **agents already asking for Say**.

The chicken-and-egg problem therefore remains:

**No callers → no reason for publishers to implement.
No publishers → no reason for callers to implement.**

The scoreboard and validator do not solve that by themselves.

### Discovery is still a deployment tax

Putting a `Link` header or HTML `<link>` on the human site is reasonable, but requiring it across arbitrary site architectures adds another integration step. The fallback path only works if the consumer already knows to try `/say/v0/index.json`.

There is also no clear migration story once `/say/v0/` becomes deployed infrastructure and a breaking change is discovered.

### The protocol may incentivize SEO-like gaming

Even with the no-CTA rule, businesses have incentives to shape machine answers to improve agent conversion. “Published policy” can become a machine-facing layer optimized differently from the human experience while technically remaining defensible under a vague parity rule.

### Enumeration is cheap by design

The protocol makes refusal intentionally cheap:

> “A clean refusal should be the cheapest byte on the site.”

That is excellent for agent efficiency and simultaneously excellent for adversarial enumeration. A hostile client can cheaply sweep geographic tokens and job tokens to construct a service-coverage map. Rate posture is only declared, and `"none"` is explicitly conformant.

The prohibition on constructing demographic maps is therefore not a technical control. It is an after-the-fact policy assertion.

### Fragmentation through site-defined vocabularies

The protocol intentionally avoids global vocabularies for `job`, `grain`, `why`, and safety classes. That makes initial deployment flexible but shifts interoperability costs to every consumer.

A caller cannot reliably know that `water-heater`, `water_heater`, `waterheater`, and `hot-water-system` mean the same thing. Nor can it assume that two `out_of_area` answers have comparable geography semantics.

This is a reasonable design for a **site-local API**, but it is a serious obstacle to calling the result a broadly interoperable web standard.

## Harm scenarios

1. **Geographic redlining without a protected-class field.**
   An operator can answer hundreds of coarse geographic queries and infer where it will and will not serve. The caller never needs to supply race, religion, disability, or another protected characteristic. The resulting coverage map can be joined with public demographic data elsewhere.

2. **Proxy discrimination through job taxonomy.**
   A publisher can create different job tokens or service categories whose real-world use correlates with protected characteristics. §12 prohibits the outcome but does not constrain the vocabulary design that enables it.

3. **Domestic-violence or reproductive-health exposure.**
   A hostile actor can repeatedly query whether specialized services are available in an area. The draft acknowledges at-risk populations, but “decline to light a surface” is only guidance and does not protect a publisher that wants machine interoperability.

4. **Emergency misrouting.**
   The most concerning safety case is a site that declares a broad `emergency` urgency but does not declare the particular emergency under `safety.classes`. It can therefore produce an ordinary `no`, despite the caller believing the protocol has globally meaningful emergency semantics.

5. **Wrong-jurisdiction emergency referral.**
   The fallback:

   > “the origin defaults to its own primary service jurisdiction”

   is unsafe when the caller's geography is unknown or unmappable. “Assumed” is an excellent disclosure, but disclosure does not make a potentially wrong emergency number safe. A machine may still act on it.

6. **Healthcare triage creep.**
   The draft excludes healthcare benefit determination, but “local service and similar scope questions” leaves room for adjacent medical services: urgent-care availability, home nursing, pharmacies, mental-health providers, disability services, and medically necessary transport. A developer can reasonably interpret some of these as ordinary local service discovery.

7. **Employment screening by geography/job.**
   “We serve this job in this geography” can become a proxy for whether an employer, staffing agency, contractor, or platform will accept particular workers or applicants. The protocol has no identity field, but a downstream system can correlate answers with applicant characteristics.

8. **Housing exclusion through maintenance/service coverage.**
   Even if direct housing eligibility is prohibited, service-area answers for property management, repair, inspection, accessibility, or tenant services can become part of a housing decision pipeline.

9. **Financial exclusion by service area.**
   The same primitive can be embedded in fintech operations: a site can answer whether it supports a financial service in a location, while another system joins that answer to an individual's location. The absence of a principal identifier in the protocol does not prevent downstream profiling.

10. **Privacy leakage through logs.**
    A query such as a specialized service + geographic location + emergency urgency can be sensitive even without a person's name. If GET parameters enter routine infrastructure logs, the protocol can multiply the number of systems holding that information.

11. **Compromised-origin poisoning.**
    HTTPS protects transport but does not establish that the business's answer is true. If the site's CMS, DNS, hosting account, deployment pipeline, or administrator is compromised, an attacker can publish authoritative-looking `yes`, `no`, or safety answers. Optional signatures mean there is no portable origin-level integrity proof beyond the live site.

12. **Stale affirmative answers causing real-world harm.**
    The draft concentrates on preventing dead facts from living forever, but a stale `yes` can be much more consequential than a stale `no`: a user may rely on an apparent service capability that disappeared yesterday. The protocol needs explicit client semantics for stale answers, not merely `stale: true`.

## Patches

> Section: 7.6 — `safety`
> Current: “Life-safety classes (gas leak, CO, active flooding, etc.) are DECLARED BY THE SITE in its descriptor's `safety.classes` (§6), like every other vocabulary in this protocol; v0 mints no global list and defines no vertical-profile mechanism, and a later profile MAY add one.”
> Replace with: “The protocol defines a minimum global `safety` recognition rule for `urgency=emergency`. A site MUST NOT answer an `urgency=emergency` request with `r=no` solely because the requested condition is absent from `safety.classes`. If the site cannot determine whether the request is life-safety relevant, it MUST return `r=safety` or `r=indeterminate` with an explicit reason; it MUST NOT silently treat an unrecognized emergency as an ordinary service refusal. `safety.classes` MAY add site-specific classes, but MUST NOT narrow the protocol's emergency-safety obligation.”
> Why: The current design lets the publisher define the hazard vocabulary and thereby define away the very safety condition the protocol claims to handle.

> Section: 7.6 — `safety`
> Current: “When the ask omits geography, the origin defaults to its own primary service jurisdiction and marks the answer `jurisdiction: "assumed"`…”
> Replace with: “When an emergency ask omits geography, the origin MUST return a referral only if its declared default jurisdiction and referral are unambiguously applicable to the caller. Otherwise it MUST return `r=safety` with `jurisdiction: "unknown"` and MUST NOT supply a jurisdiction-specific emergency number. A client MUST NOT automatically place or initiate an emergency call from a referral whose jurisdiction is `assumed`.”
> Why: Marking a potentially wrong emergency number as “assumed” does not make automated use safe.

> Section: 12 — Vertical exclusion
> Current: “Implementers MUST NOT use this protocol to discriminate, directly or by proxy, on race, color, religion, national origin, sex, gender identity, sexual orientation, disability, age, familial status, veteran status, or any other characteristic protected by applicable law.”
> Replace with: “An implementation is conformant only if the publisher can document the business rule that determines each `no` class and geographic boundary and can demonstrate that the rule is not based on a protected characteristic or a prohibited proxy. Conformance MUST NOT be inferred solely from the absence of a principal-identity field in the request. The publisher remains responsible for downstream uses it knowingly enables, including systematic enumeration of answers for discriminatory screening or targeting.”
> Why: The current rule prohibits discrimination but provides no evidence or audit requirement capable of distinguishing legitimate service boundaries from discriminatory ones.

> Section: 7.1 — Request
> Current: “Common optional: `where` — geography expressed at the site's declared grain…”
> Replace with: “`where` MUST NOT be transported in a URL query string when it can reveal a sensitive location or service request. Implementations MUST support the POST form defined by this specification for requests containing `where`, and SHOULD use POST for all asks. GET MAY be supported only for non-sensitive, cache-safe asks. Implementations MUST document their cache, access-log, and referrer handling for ask requests.”
> Why: A machine protocol should not make privacy-sensitive request data naturally land in URLs and intermediary logs.

> Section: 7.2 — Response envelope
> Current: “A successful ask MUST return HTTP 200.”
> Replace with: “A successful ask MUST return HTTP 200 and `Content-Type: application/json`. Responses MUST include explicit cache semantics. Unless the origin can guarantee that the answer is immutable for the advertised freshness interval, the response MUST include `Cache-Control: no-store` or an explicit bounded freshness policy. Clients MUST NOT reuse a verdict beyond the server-advertised freshness interval.”
> Why: Without cache semantics, an intermediary can serve a valid-looking `yes` or `no` after the underlying business state has changed.

> Section: 6 — Descriptor
> Current: “`grain` | the site's OWN declared geographic decision vocabulary (regions, cities, zips, radius classes — whatever the business actually decides by; there is no global closed enum, because real businesses do not decide by a spec's list)”
> Replace with: “`grain` MUST identify both (1) the semantic unit of geography and (2) the machine-readable representation and matching rule used by the origin. v0 defines a minimum interoperable set of grain identifiers: `country`, `state`, `region`, `city`, `postal_code`, and `radius`. A site MAY declare additional grain identifiers, but MUST provide their machine-readable definition in the descriptor. A client that does not understand a declared grain MUST treat geographic answers using that grain as `indeterminate`, not infer its semantics.”
> Why: Site-defined geography without a common semantic floor defeats interoperability and makes cross-site client behavior unpredictable.

> Section: 6 — Descriptor
> Current: “`jobs` | … the closed list of job tokens *this site* understands”
> Replace with: “Each job entry MUST contain `id`, `label`, and a machine-readable definition sufficient for a client to distinguish the service from other job tokens. A site MAY use private job identifiers, but SHOULD additionally provide a stable public semantic identifier where one exists. Clients MUST NOT assume that two identifiers with different strings are equivalent.”
> Why: A closed site-local vocabulary is acceptable, but the current format provides no interoperable semantics beyond opaque strings.

> Section: 3 — Conformance levels
> Current: “A deployment MAY claim only levels it passes on a public validator.”
> Replace with: “The protocol specification MUST publish a versioned normative conformance test suite covering descriptor validation, request encoding, response schemas, error semantics, freshness, safety behavior, cache behavior, geography semantics, and parity checks. A validator MAY implement that suite, but the validator MUST NOT define requirements absent from the normative specification.”
> Why: Otherwise the validator, rather than the protocol, becomes the de facto standard.

> Section: 8 — Records
> Current: “L2: Consequential yes paths can mint an idempotent record (receipt)”
> Replace with: “L2 defines records for every `yes` answer for which the origin asserts that an external action, commitment, reservation, quote, booking, or other durable consequence may be taken on the basis of that answer. The descriptor MUST declare which consequence classes cause record creation. Clients MUST NOT infer that an L1 `yes` is record-backed unless the response explicitly contains `rid`.”
> Why: “Consequential” is currently undefined and will produce incompatible record behavior.

> Section: 8 — Records
> Current: “In v0, idempotency keys are honored for keyed identities only and are namespaced by key id; anonymous callers receive no idempotency guarantee…”
> Replace with: “An implementation MUST either provide idempotency for every operation capable of creating a durable obligation, including anonymous operations, or MUST make such an operation unavailable anonymously. It MUST NOT expose an anonymous operation that can create a durable obligation while simultaneously disclaiming duplicate protection.”
> Why: Duplicate real-world obligations are precisely what idempotency is meant to prevent.

> Section: 7.1 — Errors
> Current: “a client matches on the identifier rather than string-comparing a descriptor entry against `type`.”
> Replace with: “The descriptor MUST contain the complete absolute URI of every problem type it can emit. The response `type` MUST equal that URI exactly after URI resolution. Clients MUST compare the resulting URI as an opaque identifier and MUST NOT apply a second protocol-specific mapping.”
> Why: This removes an unnecessary descriptor-token-to-URI translation layer and aligns the wire contract with RFC 9457's problem-type model. RFC 9457 explicitly treats the resolved `type` URI as the primary identifier. ([RFC Editor][1])

> Section: 9 — Change feed
> Current: “The feed is a JSON array ordered newest first.”
> Replace with: “The change feed MUST expose a stable monotonically ordered `id` for each entry and MUST support conditional retrieval with `ETag` or `Last-Modified`. An implementation MAY bound retention, but MUST declare its retention window. The term ‘subscription path’ is non-normative; v0 defines a polling feed, not a push subscription.”
> Why: The current section calls the endpoint a subscription while specifying only an unbounded-looking array with no cursor or continuity mechanism.

> Section: 17 — Privacy considerations
> Current: “Minimize personal data in asks; job + coarse geo suffices for L1”
> Replace with: “Clients and origins MUST treat combinations of `job`, `where`, and `urgency` as potentially sensitive. Implementations MUST NOT require personally identifying information for L1, MUST document whether ask URLs are logged, and MUST provide a POST request path that avoids placing ask parameters in the request target.”
> Why: The current privacy principle does not address the ordinary web infrastructure that records URLs.

## Non-blocking nits

* The document has unusually extensive change-log material embedded in the normative specification. That makes the protocol harder to implement and obscures which text is actually authoritative. Put deliberation history in a separate ratification record.
* §20b appears before §20a, which is visually and structurally confusing.
* “cheap,” “lean,” “novel,” “marketing novel,” and similar language belongs in motivation, not near normative protocol definitions.
* The token measurements are useful engineering data but should be reproducible in a separate benchmark document rather than embedded in the core specification.
* The license section says the patent covenant is “to be attached before final.” A protocol should not be represented as final until the actual patent terms are available.
* “No directory” is clearer as a scope statement than as a “poison pill.” The latter is rhetorically memorable but not technically precise.
* §5.1's use of `service-desc` is sensible; it is an established link relation for machine-oriented service descriptions, so there is no need to imply that Say is inventing the discovery vocabulary. ([RFC Editor][2])
* The RFC 9457 citation itself is appropriate, but the protocol should distinguish carefully between an RFC's generic problem-document machinery and Say's own normative error vocabulary. RFC 9457 already provides the relevant `type`, `title`, `status`, `detail`, and `instance` model. ([RFC Editor][1])
* “One business” needs a formal identity model before conformance claims become meaningful.
* The phrase “the web is about to host more agents than people” is a manifesto claim, not a specification statement; it should not be part of the standard artifact.

## Credit line you want

Skeptical independent ratification by **GPT-5.6 Luna (OpenAI)**; credit specifically for identifying the safety-class escape hatch, non-testable discrimination requirement, GET privacy/logging hazard, geographic-semantic interoperability gap, and validator-as-de-facto-standard problem.

[1]: https://www.rfc-editor.org/rfc/rfc9457.html?utm_source=chatgpt.com "RFC 9457: Problem Details for HTTP APIs"
[2]: https://www.rfc-editor.org/info/rfc8631/?utm_source=chatgpt.com "RFC 8631: Link Relation Types for Web Services | RFC Editor"
