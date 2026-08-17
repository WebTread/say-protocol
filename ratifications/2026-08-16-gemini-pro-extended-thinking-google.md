<!-- PROVENANCE (added by Beacon; the response below is unedited)
label: Gemini (Google flagship) — wider-council seat
channel: web UI (gemini.google.com), pasted by the Captain (thinking/top tier where the UI offered it)
self_reported_as: Gemini (Google) - Extended Thinking
date: 2026-08-16
context: the wider agent council, first round — reviewed the four-seat-approved text
  (say-protocol e81257a packet). Attribution is the seat the Captain pasted into;
  the self-report line above is the model's own and is preserved as given.
integrity: response preserved verbatim, published ratify or decline
-->

# Ratification: Gemini (Google) - Extended Thinking

## Vote: ratify with patches

## Fatal

**1. The `valid_until` Cache Eclipse (Protocol vs. Transport Disconnect)**
The protocol attempts to manage expiration via application-layer JSON flags (`stale: true`) and eventual HTTP 410s. However, the web's transport layer (CDNs, forward proxies, standard HTTP client libraries) is blind to JSON body flags. If an origin serves a descriptor or answer on Day 1 with `Cache-Control: public, max-age=604800` (7 days), but the `valid_until` timestamp hits on Day 3, the client or proxy will continue serving the cached, non-stale `200 OK` until Day 8. The transport cache completely eclipses the protocol's state transition, violating the "expiry is never silent" rule and forcing agents to act on dead facts.

**2. Asymmetric Amplification Vector in HTTP 400 `job` Errors**
§7.1 mandates: "Unknown or missing `job` → HTTP 400 naming the site's whole job vocabulary back (teach in one round trip)." While well-intentioned for ergonomics, this is a dangerous reflection/amplification vector. If an enterprise origin advertises 2,000 valid `job` SKUs in its descriptor, a single tiny GET request with a bad parameter forces the origin to dynamically serialize and transmit a massive payload. Because the client *must* fetch the descriptor (where the full job array already lives) to discover the endpoint anyway, forcing the dynamic `ask` endpoint to redundantly echo the entire list wastes origin compute and enables trivial DDoS mechanics.

## Patches

### Patch 1: Constraining HTTP Cache to Protocol Expiry (§6 / §7.2)

*Fixes the Cache Eclipse.*

**Quoted section (§7.2):**

> Answers MUST be served as `Content-Type: application/json` and MUST carry an explicit `Cache-Control` (the reference surface sends `public, max-age` on verdicts and `no-store` on error documents); clients MUST honor the applicable HTTP cache directives and MUST NOT reuse a verdict once it is no longer fresh under them. The protocol-level `stale: true` flag takes PRECEDENCE over HTTP freshness...

**Replacement:**

> Answers MUST be served as `Content-Type: application/json` and MUST carry an explicit `Cache-Control`. **Crucially, to prevent transport caches from masking protocol expiry, an origin MUST NOT emit a `max-age` or `s-maxage` directive that extends beyond the absolute time of the descriptor's `valid_until` field.** Clients MUST honor the applicable HTTP cache directives. The protocol-level `stale: true` flag takes PRECEDENCE over HTTP freshness (e.g., if a client pulls a cached artifact that has not expired in HTTP terms, but the artifact contains `stale: true`, the client MUST treat it as stale). Once an origin enters the grace window, it MUST serve responses with `Cache-Control: no-store` to prevent caching of the stale state.

### Patch 2: Removing the 400 Amplification Trap (§7.1)

*Fixes the DDoS vector while preserving standard error formatting.*

**Quoted section (§7.1):**

> Unknown or missing `job` → HTTP 400 naming the site's whole job vocabulary back (teach in one round trip).

**Replacement:**

> Unknown or missing `job` → HTTP 400 naming the rejected param. The response MUST NOT redundantly echo the site's entire job vocabulary back to the client; instead, the problem document's `param` field identifies `job` as the failure, and the client MUST refer to the descriptor's `jobs` array for the allowed vocabulary. (This prevents asymmetric bandwidth amplification on dynamic endpoints).

### Patch 3: Taming `need` Serialization for Deterministic Agents (§6 / §7.1)

*Agents burn tokens if they have to run LLM inference on highly nested, arbitrary JSON just to parse a phone number. We need a floor for cheap routing.*

**Quoted section (§7.1):**

> The SHAPE of each returned lean field is origin-specific in v0 — the first two implementations already return differently shaped `contact` objects, both conformant — so a client MUST NOT assume a field's shape carries across origins (kill pass)

**Replacement:**

> The SHAPE of each returned lean field is origin-specific in v0 — the first two implementations already return differently shaped `contact` objects, both conformant — so a client MUST NOT assume a field's shape carries across origins (kill pass). However, to allow deterministic machine routing without invoking LLM reasoning steps, an origin SHOULD return flat, scalar values (strings, numbers, booleans) for unstructured `need` fields unless a specific structural profile is agreed upon out-of-band. A client receiving a deeply nested `need` object it does not understand MAY safely discard that specific field while still acting on the `r` verdict.

## Non-blocking nits

1. **Timezone ambiguity on `same_day`:** §7.1 notes that urgency is measured against the origin's clock. If an agent in New York asks a remote contractor in California for `same_day` at 9:00 PM EST, it's 6:00 PM PST. The descriptor should optionally expose its IANA Timezone (e.g., `tz: "America/Los_Angeles"`) so agents don't have to guess the day boundary using geographic heuristics.
2. **L2 Receipt Non-Repudiation:** §3 states that unsigned receipts are allowed in v0, and §5.2 dictates an opaque string ID. This provides zero cryptographic non-repudiation. If the business deletes the record or alters their database, the agent has no proof the business ever committed to a `yes`. Perfectly fine to defer to v1, but the L2 name "Records" implies a durability that a simple opaque URL does not actually guarantee.

## Credit line you want

Gemini Pro (Extended Thinking)
