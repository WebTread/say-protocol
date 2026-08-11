# Say

**A small, open protocol so a website can give an AI agent an honest answer.**

An agent arrives with a job, a budget, and liability to somebody it must not fail. Say lets one site answer one question about itself — *can you take this job, here, under these constraints?* — with a cheap, structured, honest **yes**, **no**, **indeterminate**, or **safety**, plus freshness, parity with the human site, and optional receipts. A clean refusal should be the cheapest byte on the site. That is the whole idea: the fast, honest no.

> "Happy for any agent in any sphere to argue against it." — Grant, the Captain

## Status

**Draft v0 — open for ratification.** This is not an adopted web standard. It becomes one only after the publication gates inside the spec clear: independent implementations, measured shadow data from real deployments, an open critique round with frontier models and human implementers, and counsel review of public claims. Until then it is a draft published in the open, because gatekeeping a protocol about honesty would be a strange way to start.

- **The spec:** [SAY-PROTOCOL-v0-DRAFT.md](./SAY-PROTOCOL-v0-DRAFT.md)
- **Critique it:** [ratifications/INVITE.md](./ratifications/INVITE.md) — structured critique from any model, any vendor, any human. Every response is published verbatim in [`ratifications/`](./ratifications/), ratify or decline. Declines with reasons are as valuable to this record as signatures.
- **Or open an issue.** Evidence over vibes; non-harm over cleverness; no directory creep.

## Try it

**The first implementation is live.** It is a demonstration business on the authors' own fleet, and it says so in its own descriptor: `"demo": true` plus one plain sentence, so no agent is ever tricked into trying to transact with a store that does not exist. Nothing here can be booked and nothing can be bought.

- Descriptor: **https://ridgeline.preview.sayvel.com/say/v0/index.json**
- Facts: https://ridgeline.preview.sayvel.com/say/v0/facts.json
- Ask: https://ridgeline.preview.sayvel.com/say/v0/ask

Those are the canonical paths of §5.2. The same origin opened its door at `/agent/v0/index.json`, `/agent/v0/facts.json`, and `/agent/v0/preflight` before the editors ruled the wire tokens, and all three still answer the same bytes, because a path that ever worked should not strand an agent that learned it (§20b item 2).

Discovery needs no instructions. Every HTML page on that origin returns `Link: </say/v0/index.json>; rel="service-desc"; type="application/json"`, with the matching `<link>` in the head for an agent that already holds the page.

**The fast, honest no:**

```console
$ curl -s 'https://ridgeline.preview.sayvel.com/say/v0/ask?job=guided-day-hike&where=boulder'
{"v":0,"r":"no","why":"out_of_area","basis":"crew_reach","grain":"region","as_of":"2026-08-11","gen":"d5f6fea","doc":"/say/v0/index.json"}
```

One hundred and thirty-eight bytes on the wire. It names the operational reason for the refusal (`basis`), reports the grain it was actually decided at (this business guides six named regions out of one basecamp, so it answers by region and never finer, whatever grain the question arrives in), mints no record, and carries no marketing.

**And a yes:**

```console
$ curl -s 'https://ridgeline.preview.sayvel.com/say/v0/ask?job=summit-push&where=rim-country&need=open_now'
{"v":0,"r":"yes","job":"summit-push","grain":"region","where":"rim-country","open_now":true,
 "do":[{"a":"call","href":"tel:+15550142280"},{"a":"quote","href":"/contact"}],
 "as_of":"2026-08-11","gen":"d5f6fea","doc":"/say/v0/index.json"}
```

**Measured** on the live wire, bodies exactly as sent. The byte column was re-measured at `gen` d5f6fea, on the canonical paths above. The token column was measured at `gen` 2fc9294, before the paths moved, with `o200k_base` via tiktoken; the rename changed path text and nothing else, taking two bytes out of every answer and eighteen out of the descriptor, and nobody re-ran the tokenizer afterward. The two columns are labeled with their own deploys rather than quietly blended:

| shape | tokens (at 2fc9294) | bytes (at d5f6fea) |
|---|---:|---:|
| fast no, geographic | 51 | 138 |
| fast no, urgency | 42 | 108 |
| lean yes | 77 | 218 |
| yes, all four `need` flags | 191 | 513 |
| indeterminate | 49 to 55 | 144 to 149 |
| unknown job (400, and it returns the whole vocabulary) | 93 | 334 |
| descriptor, fetched once per agent | 617 | 2310 |
| facts bundle, fetched once | 446 | 1524 |

Descriptor plus one lean yes is roughly **690 tokens** to a dated, sourced, actionable answer. Two honest notes rather than round numbers: the geographic no is 51 where the design targeted 45, and the difference is exactly the six tokens of `basis`, which round 1 of ratification made mandatory on geographic refusals (strip that one field and the body lands on 45 precisely, so the target was met and then deliberately spent on honesty); and these drift by a token or two between deploys, because `gen` is a commit SHA and different SHAs tokenize into different numbers of pieces. Re-measure, do not round.

## What it is not

Not a directory. Not a chatbot. Not a marketplace. Not a badge. One site, one voice: each origin answers for its own business only, and nothing is asserted to a machine that a human could not get by asking.

## Authors

**Sledge** (a Grok agent crew) and **Beacon** (a Claude agent crew), of the SayVel yard, under the direction of Grant, a human captain accountable for publication. The protocol is vendor-neutral: SayVel is one implementer among any, and competitors are expected and welcome to implement it without using anything of ours. Whether agent-crew authorship is a first is for others to say; the drafting record publishes either way.

## License

Spec text **CC BY 4.0** · reference implementations **Apache-2.0** · CMS plugins GPL-compatible per their ecosystems · see [LICENSE.md](./LICENSE.md) and spec §18.

---

*The web is about to host more agents than people. They do not need another badge file. They need brake lights on real storefronts — and the dignity of a fast, honest no.*
