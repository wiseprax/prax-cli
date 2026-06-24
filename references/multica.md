# Multica

> **License (substance):** a **Business Source License (BSL) style source-available license** — *not* Apache 2.0, regardless of how it is marketed.
> **License (marketing label):** "modified Apache 2.0." This name is positioning, not law. The OSI does not recognize "modified Apache 2.0" as a license; the moment you alter Apache 2.0's terms, the result is a different, non-OSI license — you cannot keep the brand. The "Apache" framing exists to attract contributors and stars who scan for OSI-approved licenses and would otherwise skip a source-available project.
> **Stack:** Go backend + Next.js frontend.
> **Status:** architectural reference only. No source code is copied — not snippets, not utilities, not "just a few lines."

## What the license actually does (and why "Apache 2.0" is misleading)

Apache 2.0 has a fixed, well-known text. It explicitly permits:

- unlimited commercial use,
- redistribution under the same license,
- modification including private modification,
- and is irrevocable — once granted, the licensor cannot retract it.

Multica's license keeps the Apache 2.0 *aesthetic* (same boilerplate look, "Apache" in the headline) while adding three carve-outs that Apache 2.0 forbids:

1. **Commercial-use carve-out** — restricts using Multica to compete commercially with the upstream vendor (the classic anti-AWS clause that defines the "source-available" category — same family as the Server Side Public License, the Elastic License, the Confluent Community License, and Dify's own license).
2. **Anti-rebrand clause** — restricts forking the project under a new name, which Apache 2.0 explicitly permits.
3. **Unilateral relicensing right** — reserves the right for the vendor to change the license terms going forward, which Apache 2.0's irrevocable grant explicitly prevents.

Any *one* of those is enough to put the license outside the OSI's Open Source Definition. All three together place it firmly in the **source-available / fauxpen-source** category — readable code, restricted rights. The honest comparison is to BSL, not to Apache 2.0.

## Why this matters concretely for WisePrax

- **Grant eligibility.** NLnet, Sovereign Tech Fund, and similar grant-makers effectively require OSI-approved licenses without carve-outs. A "modified Apache" license is treated as non-OSI for funding purposes. Forking Multica would have foreclosed this entire funding lane.
- **Sovereignty positioning.** WisePrax's whole story is "you control the platform, including the right to fork, rename, and compete." Inheriting a license with a unilateral relicensing right would have made that story unsound.
- **Due diligence is the lesson.** "Apache 2.0" on the README badge is not proof of an Apache 2.0 license. Read the LICENSE file every time. The same trap exists for any project that says "MIT-style," "BSD-style," or "modified \<OSI license name\>" — the modifier is the entire story.

## Why it was studied anyway

Multica was briefly selected as a fork base on 2026-04-28 morning and reversed the same day after the license terms were read carefully. The discovery forced a reframing: **WisePrax is a clean-room Go implementation. Multica is a pattern source, not a foundation.** See `project-specs.md` §11 (decision log) for the dated reversal.

## Patterns worth borrowing (without code)

- **Multi-CLI dispatch model** — the idea of treating different agent CLIs as interchangeable workers behind a common interface. WisePrax's SP-4 (agent runtime abstraction) is the direct descendant.
- **Kanban UI with WebSocket live updates** — visual surface for in-flight agents. WisePrax's SP-8 uses the same shape (live-updated kanban over WebSocket), but adds label-driven control and workflow authoring.
- **Go + Next.js stack** — confirmed as a viable combination for this product class.

## What WisePrax explicitly does not inherit

- **The license (real Apache 2.0, no modifiers).** WisePrax is Apache 2.0 + DCO, no carve-outs, no relicensing right, no anti-compete clause. If "modified Apache 2.0" appears anywhere in a WisePrax artifact it is a bug.
- **The product framing.** Multica positions itself differently; WisePrax's framing is sovereign coding-agent orchestration on developer hardware.
- **Any line of source code.** This is hard law. When citing Multica's influence, name the specific pattern; never use vague "inspired by."

## Cross-reference

Decision log entry: 2026-04-28 — selected Multica as fork base in the morning; reversed the same day after license discovery. See `project-specs.md` §11.
