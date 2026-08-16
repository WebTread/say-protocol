<!-- PROVENANCE (added by tools/ratify.mjs; the response below is unedited)
model: @cf/mistralai/mistral-small-3.1-24b-instruct
label: Mistral Small 3.1 (Mistral AI)
channel: Cloudflare Workers AI (OpenAI-compatible endpoint)
requested_at: 2026-08-16T18:51:02.766Z
prompt_tokens: 9582
completion_tokens: 1180
neurons: 365.0116882324219
seconds: 32.8
finish_reason: stop
self_reported_as: Mistral AI
integrity: response preserved verbatim, published ratify or decline
-->

# Ratification: Mistral AI

## Vote
ratify with patches

## Principle breaks
None identified.

## Fatal flaws
None.

## Ambiguities that will split implementations
1. **Section 5.2: Paths**
   - **Current:** "Legacy preview deployments MAY still serve `/agent/v0/*` with a `preflight` ask segment. New deployments SHOULD use `/say/v0/*` and `ask`."
   - **Issue:** The use of "MAY" and "SHOULD" can lead to ambiguity. Some implementations might interpret this as optional, while others might see it as a requirement for new deployments.
   - **Suggested Patch:**
     > Section: 5.2 Paths
     > Current: "Legacy preview deployments MAY still serve `/agent/v0/*` with a `preflight` ask segment. New deployments SHOULD use `/say/v0/*` and `ask`."
     > Replace with: "Legacy preview deployments MAY still serve `/agent/v0/*` with a `preflight` ask segment. New deployments MUST use `/say/v0/*` and `ask`."
     > Why: This clarifies that new deployments should strictly adhere to the new path structure.

2. **Section 7.1: Request (conceptual)**
   - **Current:** "Unknown or missing `job` → HTTP 400 naming the site's whole job vocabulary back (teach in one round trip)."
   - **Issue:** The term "teach in one round trip" is ambiguous. It is unclear whether this means the client should immediately understand the vocabulary or if it should resend the request with the correct job.
   - **Suggested Patch:**
     > Section: 7.1 Request (conceptual)
     > Current: "Unknown or missing `job` → HTTP 400 naming the site's whole job vocabulary back (teach in one round trip)."
     > Replace with: "Unknown or missing `job` → HTTP 400 naming the site's whole job vocabulary back. The client MUST resend the request with the correct job from the provided vocabulary."
     > Why: This clarifies the expected behavior for the client when an unknown or missing `job` is encountered.

## Adoption hazards
1. **Publisher-only rot:** There is a risk that websites will implement the Say protocol but no agents will consume the data, leading to a situation where the protocol is published but not used. To mitigate this, the protocol should include incentives or mechanisms for ensuring that agents actively consume the data.
2. **Complexity of implementation:** The protocol involves multiple levels of conformance and various endpoints. This complexity might deter smaller businesses or less technically savvy implementers from adopting the protocol.
3. **Dependency on validators:** The protocol relies heavily on validators to ensure conformance. If validators are not widely available or trusted, the protocol's effectiveness could be compromised.

## Harm scenarios
1. **Discrimination by area or service:** Although the protocol includes provisions to prevent discrimination, there is still a risk that implementers could find ways to discriminate indirectly. For example, by setting arbitrary service areas or using vague refusal reasons.
2. **Regulated verticals:** The protocol explicitly excludes certain regulated verticals, but there is a risk that implementers could misuse the protocol in these areas. For example, a housing provider could use the protocol to discriminate against certain applicants by claiming they are "out of area."
3. **Privacy concerns:** The protocol involves the exchange of geographic and job-related data. There is a risk that this data could be misused or leaked, leading to privacy violations.

## Patches
1. **Section: 5.2 Paths**
   - **Current:** "Legacy preview deployments MAY still serve `/agent/v0/*` with a `preflight` ask segment. New deployments SHOULD use `/say/v0/*` and `ask`."
   - **Replace with:** "Legacy preview deployments MAY still serve `/agent/v0/*` with a `preflight` ask segment. New deployments MUST use `/say/v0/*` and `ask`."
   - **Why:** This clarifies that new deployments should strictly adhere to the new path structure.

2. **Section: 7.1 Request (conceptual)**
   - **Current:** "Unknown or missing `job` → HTTP 400 naming the site's whole job vocabulary back (teach in one round trip)."
   - **Replace with:** "Unknown or missing `job` → HTTP 400 naming the site's whole job vocabulary back. The client MUST resend the request with the correct job from the provided vocabulary."
   - **Why:** This clarifies the expected behavior for the client when an unknown or missing `job` is encountered.

## Non-blocking nits
1. **Section 10: Parity and robots**
   - **Current:** "Machine surfaces SHOULD send `noindex` (or equivalent) when they are compact twins of human content, and MUST NOT cloak contradictory substance."
   - **Suggestion:** Consider making the `noindex` directive a MUST to ensure that machine surfaces do not interfere with search engine indexing of human content.
2. **Section 16: Security considerations**
   - **Current:** "All protocol endpoints MUST be served over HTTPS."
   - **Suggestion:** Add a requirement for HTTP Strict Transport Security (HSTS) to further enhance security.

## Credit line you want
Mistral AI