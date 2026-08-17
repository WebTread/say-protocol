<!-- PROVENANCE (added by Beacon; the response below is unedited)
model: Meta AI web (self-reported earlier as Muse Spark 1.1)
label: Meta AI (Meta flagship) — READ-BACK CLOSE after round 4
channel: web UI (meta.ai), same conversation, pasted by the Captain
date: 2026-08-16
context: shown the disposition of its round-4 critique. It independently pulled the
  patched draft and VERIFIED each of its folds against the live text, line numbers
  quoted, then closed with no further block on the revision. No formal re-vote line was
  given; the close reads as satisfied ratification of the revision, and is filed as
  written rather than paraphrased into one.
integrity: response preserved verbatim, published ratify or decline
-->


Appreciate the verbatim filing and the same-night sit — and the credit in the log.

I just pulled the patched draft you linked and it matches what you summarized:

- **same_for_all is now MUST** — line 243 `v0 MUST declare "same_for_all"` and `MUST NOT vary a verdict or a refusal's basis by tier` — Principle 2 loophole closed, as filed.

- **Slug alphabet exact** — line 216 `every maximal run of characters not in [a-z0-9] collapsed to one hyphen` — round-4 precision, Meta — that's the split I flagged.

- **Stale scope pinned** — `every response body served under /say/v0/* in the window MUST carry the same flag` plus `Cache-Control: no-cache` — and the important fix:

- **410 trap** — `SHOULD serve 410... MUST serve 404 and MUST NOT serve a 200 — consumers treat either non-200 as ABSENT after expiry, and the forbidden thing was always the stale 200, not the status flavor` — exactly. You caught your own shared-hosting-floor law and the spec yielded. That's how it should work.

- **Same-site** — `MUST be same-site, SHOULD same-origin, consumers reject cross-site` — fixes the www-vs-apex reality.

Good disposition writeup too — especially keeping self-misidentifications footnoted rather than quietly corrected. That's why a human Captain matters.

No further block from me on this revision. The door staying open is right — if I hit another divergence in the wild I'll file another verbatim critique the same way.
