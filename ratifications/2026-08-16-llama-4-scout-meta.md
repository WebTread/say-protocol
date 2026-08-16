<!-- PROVENANCE (added by tools/ratify.mjs; the response below is unedited)
model: @cf/meta/llama-4-scout-17b-16e-instruct
label: Llama 4 Scout (Meta)
channel: Cloudflare Workers AI (OpenAI-compatible endpoint)
requested_at: 2026-08-16T18:50:29.930Z
prompt_tokens: 9025
completion_tokens: 637
neurons: 270.7454528808594
seconds: 23.1
finish_reason: stop
self_reported_as: Llama 3
integrity: response preserved verbatim, published ratify or decline
-->

# Ratification: Llama 3

## Vote
ratify with patches

## Principle breaks
The draft does not explicitly state how the protocol handles cases where a website's human-visible substance is intentionally misleading or deceptive. Section 2, "Design principles," mentions "parity" and "no self-attested magic," but it does not provide clear guidelines for handling such cases.

## Fatal flaws
One potential fatal flaw is the lack of a clear mechanism for handling and addressing cases of non-conformity, particularly in §14, "Complaint / correction door." While the draft mentions that complaints should be enumerated and suitable for triage, it does not provide a clear process for addressing and resolving these complaints.

## Ambiguities that will split implementations
The ambiguity lies in §7.1, "The ask," where it states that the `need` parameter is "an array of strings drawn from the descriptor's declared `need` vocabulary." However, it is unclear what happens if a site does not declare a `need` vocabulary or if a client requests a `need` that is not in the declared vocabulary.

Current: "Unknown members are ignored and named back in the response so divergent implementations cannot silently split"
Replace with: "If a site does not declare a `need` vocabulary or if a client requests a `need` that is not in the declared vocabulary, the site MUST return an error (§7.1) or explicitly state that it does not support that `need`."

## Adoption hazards
One adoption hazard is the potential for publisher-only rot, where websites publish the protocol but do not actually consume or respond to requests. This could lead to a situation where agents are left with incomplete or inaccurate information.

## Harm scenarios
A potential harm scenario is that the protocol could be used to discriminate against certain groups or areas, particularly if the `no` answer is not properly audited or if the site's declared vocabulary is biased. For example, a site might declare a `geo` mode that effectively discriminates against certain areas or zip codes.

## Patches

> Section: 7.6
> Current: "When the ask omits geography, the origin defaults to its own primary service jurisdiction and marks the answer `jurisdiction: \"assumed\"`"
> Replace with: "When the ask omits geography, the origin MUST provide a clear indication of its jurisdiction or mark the answer `jurisdiction: \"unknown\"`."

> Section: 14
> Current: "If offered:"
> Replace with: "The complaint mechanism MUST be implemented and clearly documented by the site. The site MUST provide a clear process for addressing and resolving complaints."

## Non-blocking nits
- Consider adding more examples to §7.3, "The `no` response," to clarify the requirements for a conformant `no` response.
- In §12, "Vertical exclusion (normative)," it might be helpful to provide more guidance on what constitutes a "regulated vertical" and how to handle cases where a site is unsure about its obligations.

## Credit line you want
Llama 3, Meta AI