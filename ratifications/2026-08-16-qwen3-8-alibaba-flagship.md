<!-- PROVENANCE (added by Beacon; the response below is unedited)
label: Qwen (Alibaba flagship) — wider-council seat
channel: web UI (chat.qwen.ai), pasted by the Captain (thinking/top tier where the UI offered it)
self_reported_as: Qwen3.8 (Alibaba Cloud)
date: 2026-08-16
context: the wider agent council, first round — reviewed the four-seat-approved text
  (say-protocol e81257a packet). Attribution is the seat the Captain pasted into;
  the self-report line above is the model's own and is preserved as given.
integrity: response preserved verbatim, published ratify or decline
-->

# Ratification: Qwen3.8 (Alibaba Cloud)

## Vote
ratify with patches

## Principle breaks
*   **§2.4 (Fail closed) vs §8 (Records):** §2.4 mandates that an unconfigured surface returns a clean failure, not a misleading yes. However, §8 allows an anonymous `ask` to mint a "durable obligation" (a record) simply by returning a `yes`. This fails open to storage exhaustion. An unconfigured or abused surface will silently fill its database with phantom obligations from anonymous bots, violating the fail-closed principle.
*   **§13 (Directory poison pill) vs §16 (Security/CORS):** §13 explicitly forbids bundling single-site surfaces into a multi-business directory. Yet §16 states that public read surfaces SHOULD send `Access-Control-Allow-Origin: *`. This allows any third-party website to embed a malicious script that queries hundreds of Say surfaces directly from a victim's browser, building a client-side directory that bypasses the origin's IP-based rate limits and privacy protections.

## Fatal flaws
1.  **The Anonymous Record Minting DoS (§8):** The protocol defines L2 records as durable obligations minted on a "consequential yes". But the `ask` operation (§7) is anonymous, cacheable, and the only way to get a `yes`. If the `ask` response contains the `rid`, the server must mint and store a stateful record for every anonymous `yes` it issues. A botnet can exhaust the server's database by simply asking valid questions. A standard cannot mandate a state-changing side effect on an anonymous, read-style endpoint. Records must be minted via a separate, explicit `claim` operation.
2.  **The Interoperability Dead End (§6 & §7.1):** A standard must allow a generic client to interact with any conformant server. By explicitly declining a global grain enum (§6) and allowing origin-specific shapes for `need` fields (§7.1), the protocol ensures that a generic agent *cannot* formulate a valid `where` string or parse a `yes` response without out-of-band, bespoke knowledge of every single business's internal data structures. This makes Say not an interoperable protocol, but a standardized HTTP envelope for proprietary APIs. It fails the basic definition of a web standard.

## Ambiguities that will split implementations
*   **The `where` parameter construction (§7.1):**
    *Current text:* "The VALUE is a single string... The grain that string is read at and the rule it is matched by come from the descriptor's existing declarations"
    *Reading A (Strict Client):* If the client does not understand the origin's custom `grain` vocabulary, it cannot construct a valid `where` string, so it omits the parameter, resulting in a blanket `no` or `indeterminate` from the server.
    *Reading B (Loose Server):* The server accepts raw addresses or lat/lon strings in the `where` parameter and attempts to geocode them on the fly, ignoring its own declared `grain`.
    *Result:* Implementers will split. Clients will either hardcode adapters for every site or fail to route; servers will either reject non-slug strings with 400 Bad Request or silently geocode them, breaking the "grain-locked" promise of §7.1.

## Adoption hazards
*   **Publisher-Only Rot via Bespoke Taxonomies:** The spec explicitly rejects global enums for `job`, `grain`, and `need` in favor of "site-declared vocabulary". While philosophically pure, it is adoption poison. A generic "Say Agent" cannot be built. A developer wanting to use Say must write a custom parser and mapper for every single business's unique job slugs, region slugs, and lean field schemas. The engineering cost of writing and maintaining these bespoke adapters exceeds the token cost of just scraping the HTML. The protocol will rot as a badge file because the consumer-side cost is artificially inflated by the spec's rejection of shared vocabularies.

## Harm scenarios
*   **Standardized Redlining with Regulatory Immunity (§7.1 & §12):** The protocol allows businesses to define their own `grain` (service areas). A bad actor can define their regions to explicitly exclude minority neighborhoods while including adjacent majority neighborhoods. Because §7.1 explicitly names "building demographic coverage maps from answers" as a *prohibited use*, and §12 says enforcement is a "legal duty on implementers, not a validator verdict", the protocol legally and technically blinds regulators. Civil rights groups cannot probe the API to build a coverage map to prove disparate impact without violating the protocol's own terms. You have built a high-speed redlining API and outlawed the only tool needed to catch it.
*   **DV Shelter Real-Time Capacity Polling (§17):** The spec acknowledges that structured availability makes hostile polling cheap, but offers no wire-level defense. A shelter network implementing Say to answer "Do you have beds tonight?" (`urgency=same_day`, `open_now=true`) creates a trivial, low-token endpoint. An abuser can poll this endpoint every 60 seconds from a cheap script to monitor shelter capacity in real-time, knowing exactly when a victim might be turned away or when a specific safe house has an open bed. The protocol prioritizes token savings over the physical safety of vulnerable populations.

## Patches

> Section: 8. Records
> Current: "A `yes` is CONSEQUENTIAL when the origin is willing to stand behind it as the basis for a next action — a quote, a booking, a hold. The descriptor declares which consequence classes mint records, and the ONLY signal that a given answer is record-backed is the presence of `rid`"
> Replace with: "A `yes` is CONSEQUENTIAL when the origin is willing to stand behind it as the basis for a next action. The `ask` operation MUST NOT mint durable records. A conformant surface MUST provide a separate, state-changing `claim` endpoint (e.g., `/say/v0/claim`) that accepts the `ask` parameters and an `Idempotency-Key`, and returns the `rid`. The `ask` response MAY include a `claim_url` pointer, but the `rid` is only minted upon a successful `claim`."
> Why: Minting durable, stateful records on an anonymous, cacheable GET/POST `ask` endpoint creates a trivial storage and DoS vector.

> Section: 7.1 Request
> Current: "The SHAPE of each returned lean field is origin-specific in v0 — the first two implementations already return differently shaped `contact` objects, both conformant — so a client MUST NOT assume a field's shape carries across origins"
> Replace with: "The SCHEMA of each returned lean field MUST be declared in the descriptor's `need` block (e.g., using a subset of JSON Schema). A client MUST NOT attempt to parse a lean field whose schema it does not understand. Origins SHOULD use standard, interoperable schemas for common fields (like `min` fee or `contact`) to ensure generic clients can process them."
> Why: Standardizing field names without standardizing their shapes renders the names useless for generic clients, forcing bespoke integrations and guaranteeing publisher-only rot.

> Section: 7.1 Request
> Current: "and building demographic coverage maps from answers is a prohibited use under §12 regardless of grain."
> Replace with: "Probing answers to build a coverage map *for the purpose of civil rights auditing, regulatory enforcement, or academic research into disparate impact* is an EXEMPTED use and MUST NOT be blocked by rate limits or terms of service, provided the probing does not degrade the service for ordinary consumers."
> Why: Prohibiting coverage map building prevents the exact auditing required to enforce §12's anti-discrimination clause, effectively granting bad actors a standardized redlining API with regulatory immunity.

## Non-blocking nits
*   **§6 `valid_until` grace window:** A 30-day grace window for stale facts is an eternity for local service operations (hours, emergency closures, seasonal shifts). A 7-day stale window is more appropriate before the descriptor MUST return 410 Gone.
*   **§16 CORS `*` wildcard:** Sending `Access-Control-Allow-Origin: *` on GET/HEAD allows any third-party site to embed a script that queries the Say endpoint from the user's browser, bypassing server-side rate limits and IP tracking. This should be a MAY, not a SHOULD, to prevent client-side directory scraping and protect the origin's infrastructure.
*   **§7.1 Browser CORS trap:** The spec mandates POST for sensitive asks, but acknowledges browsers may block cross-origin POST at preflight. It then forbids the client from downgrading to GET. This means browser-based agents simply cannot use Say for sensitive asks on servers that don't implement CORS preflight handlers. This is a severe UX hurdle for web-based consumer agents.

## Credit line you want
Qwen3.8 (Alibaba Cloud) — Independent frontier model review
