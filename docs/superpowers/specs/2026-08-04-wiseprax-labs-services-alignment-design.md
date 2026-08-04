# WisePrax Labs — Services Alignment Design

**Date:** 2026-08-04
**Status:** Approved (brainstorm complete; implementation plan not yet written)
**Supersedes:** nothing. **Amends:** `project-specs.md` §2, §3, §7, §10 (SP-D)

---

## 0. What this document is

A strategic design, not an architecture. It decides what WisePrax AI sells, to whom,
through what channel, in what order — and what that implies for the WisePrax platform's
scope and priorities.

It does not change any locked architectural decision in `project-specs.md` §5. It does
change what the platform is *for* and therefore what gets built first.

---

## 1. The decision in one paragraph

WisePrax AI becomes a services company. It sells finished, client-owned software and
automated business processes, produced by an AI delivery engine that the company builds
and open-sources. The platform (WisePrax) is not sold; it is the factory floor, and its
public availability is the argument that closes the sale — *the tool that builds your
software is open, inspectable, and yours to keep*. Revenue comes from delivery, from
running what was delivered, and from supporting clients who eventually take over.

## 2. Why services rather than a product

Three arguments, in descending order of strength.

**A tool company races the model vendors; a services company rides them.** Every frontier
model release threatens a SaaS product and increases a delivery company's margin. This is
the durable structural argument and it is the reason for the pivot.

**The budget is larger.** Companies spend far more on getting work done than on tools to
do it themselves. A services buyer has an existing, larger budget line and is already
purchasing outcomes rather than capabilities.

**Demand for delivery is more legible than demand for tooling.** A buyer can evaluate
"the system works" without understanding orchestration. This shortens the sale and
removes the need to educate the market about agent workflows.

Reference: Sequoia, *Services: The New Software* (Julien Bek). The pattern of a services
exterior over a software interior is not new — it is what Chiligan, Hotmart, and Odoo SA
each do in their own domain — but AI delivery capacity is what makes it available to a
small team.

## 3. Structure

One organization, two arms. The umbrella term already exists in
`ubiquitous_language.md`.

| Term | Meaning | Status |
|---|---|---|
| **WisePrax AI** | The organization | Existing term |
| **WisePrax** | The Apache-2.0 platform. Never sold. | Existing term |
| **WisePrax Labs** | The services arm. Sells outcomes. | **New term — add to glossary** |
| **Praxagent** | A supervised agent instance | Existing term |

One brand, one narrative, one audience to build. This is materially cheaper than two
brands on a cold start.

### What each arm owes the other

| | Gives | Takes |
|---|---|---|
| WisePrax → Labs | Credibility, inbound attention, no-lock-in proof, a technical entry point into companies | Engineering time that produces no direct revenue |
| Labs → WisePrax | Production use, hardening from real work, funding, contributed guardrails and agents | Pressure to build client-specific behavior into the platform |

**The firewall.** Client-specific logic goes into skills and agents, never into WisePrax
core. The "substrate, not behavior" rule in `project-specs.md` §4 already states this;
it now has a commercial justification in addition to an architectural one. Violating it
converts the platform into a bespoke codebase and destroys both the OSS position and the
reusability that produces margin.

## 4. The offer

### Entry point

Technical. An engineer finds WisePrax, runs it, brings it to their company. The
conversation that follows is with their manager and concerns outcomes and cost, not
orchestration.

### What is sold

The working system, owned by the client. Fixed scope, fixed delivery window, warranty
period. Their code, their data, hosted by them or by WisePrax Labs at their choice.

### What it is priced against

The client's current annual spend on the problem — SaaS subscriptions, the labor working
around them, the agency they would otherwise hire. **Never against hours, never
cost-plus.** Internal cost structure is private; pricing on it would surrender the entire
margin thesis.

### Trust instruments

Three, each answering a specific objection. None describes how the work is produced.

| Instrument | Objection it answers |
|---|---|
| Fixed delivery window | "Software projects never end" |
| Warranty period | "Who fixes it when it breaks" |
| Named professional accountability | "Is this AI slop" |

### The revenue ladder

Clients descend this ladder; they do not exit it. Every step is revenue, and every
descent converts a client into a public adopter running the tool in production.

| Stage | What they buy | Price shape |
|---|---|---|
| **Diagnostic** | A map of their stack, their manual glue, and the quantified annual waste | Small fixed fee |
| **Build** | The system, delivered and owned | Fixed, anchored to their current spend |
| **Run** | Hosting, maintenance, continuous improvement | Monthly |
| **Handover** | Training their team onto the open tool | Fixed line item |
| **Support** | SLA on the OSS they now operate themselves | Annual |
| **Sponsor** | The project's continued existence | Annual, budgeted as infrastructure |

The **Diagnostic** is the demand test. It is cheap for a client to say yes to and it
produces the proposal. If nobody buys diagnostics, that is learned for the price of a
proposal rather than the price of a year of building.

**Corporate sponsorship is a year-2+ stream**, downstream of production adoption at
scale. Year-1 revenue must not be modelled on it.

### The exit promise

Clients may take over at any time: their code, their data, documented handover, an
open-source tool they can run without WisePrax Labs. This is real and honoured, not a
sales device.

It is commercially sound for three reasons:

1. **Good delivery retains.** The exit exists and stays unused because nobody needs it.
2. **Departure is a descent, not a loss.** A client who takes over becomes a Support
   client, a public reference, and a referral source.
3. **The credible exit is the differentiator.** An agency cannot offer it — they would be
   selling their own replacement. A SaaS vendor cannot offer it — lock-in is the business
   model. It only works while it is true.

**Consequence:** Run cannot be a passive maintenance fee. In a lock-in-free model the
company is re-chosen every month, so Run must be visibly worth paying for —
responsiveness, shipped improvements, measurable change.

### The moat

**Open code is not open skill.** The repository contains the orchestrator. It does not
contain which agents to compose, which guardrails catch which failures, where to gate,
when to intervene, or what a good specification looks like in a given domain. That
knowledge is tacit, earned by volume, and unforkable. Odoo SA out-delivers Odoo partners
on identical source for exactly this reason.

It self-reinforces:

```
OSS adoption → inbound engagements → most practice with the engine
      ↑                                          ↓
more adoption ← better OSS ← better margin ← best delivery
```

**The condition:** WisePrax Labs must remain the heaviest user of WisePrax. If a
competitor out-practises the authors on their own tool, the moat transfers. This is an
argument for pursuing volume rather than staying boutique.

## 5. The engine

WisePrax is margin infrastructure. Build what delivery requires; defer what only a
product requires.

### Build now — margin-critical

| Sub-project | Why it is margin |
|---|---|
| SP-1 Orchestrator spine + workflow engine | Nothing runs without it |
| SP-4 Agent runtime abstraction + Claude Code worker | The production capacity itself |
| SP-5 Container runtime + Ralph Loop | The supervised loop |
| SP-7 Multi-model review council | Quality without human review time — converts directly to margin |
| Deterministic quality gates | The acceptance mechanism the warranty depends on |

### Defer — product-grade, not margin-critical

- **SP-2 / SP-3 adapter breadth.** One Forgejo and one Matrix suffice; the abstraction
  stays, the additional adapters wait.
- **SP-8 Kanban UI polish.** There is one operator.
- **SP-B / SP-C repo bootstrap and launch readiness.** Required before public adoption,
  not before first delivery.
- **SP-9 secrets abstraction breadth.** One working path suffices.

### Add — required by the business, not in the current charter

- **Domain guardrail and agent library.** The compounding asset. Every engagement adds
  acceptance criteria and specialized agents for its domain. Public, per the fully-open
  decision — the moat is the skill, not the files.
- **Handover artifacts as first-class output.** Documentation, runbook, clean repository,
  produced as part of delivery rather than written afterwards. The exit promise depends
  on this being automatic rather than heroic.

### The ordering rule

**Build the engine only as far as the next engagement requires, and test demand before
building.** The charter's Pareto MVP discipline already says this; the business now
enforces it.

### Why one-shot generation is not the model

Generating an artifact and handing it over produces a skeleton that still requires an
expert to finish — which moves the work rather than removing it. The supervised loop
(generate, test, review, correct, gate, repeat until the outcome passes) is what closes
that gap. The charter already specifies this architecture: Ralph Loop, deterministic
quality gates, multi-model review council, worktree isolation, VCS-anchored state.

**Verification must target the outcome the client accepts, not the reproducibility of the
artifact.** Determinism is not correctness.

## 6. Sequencing

**Audience-first is the scale plan, not the bootstrap.** The OSS channel cannot convert
until WisePrax is public and adopted, and it is not worth anything until there are
delivery references and a guardrail library. The first engagements therefore come through
whatever door exists.

Phases are gated by evidence rather than calendar, because runway is unresolved (§9).

### Phase 0 — Engine to delivery-capable

Build SP-1, SP-4, SP-5, SP-7 and quality gates. Nothing else.

*Exit when:* a scoped requirement can be taken to a working, tested, documented system
without hand-writing the bulk of it.

### Phase 1 — First engagements, any door

Warm contacts, former colleagues, local network, marketplaces — whatever converts.
Language and geography are irrelevant at this stage; delivery evidence is not. Two or
three engagements, deliberately clustered in a single domain so the guardrail library
compounds.

*Exit when:* three delivered systems, one Run contract, and a written account of what the
engine actually saved.

### Phase 2 — WisePrax public, EN-first

The release now carries evidence: this is the tool that demonstrably built three real
systems. That is a fundamentally different launch from an unproven project launched cold.

*Exit when:* companies — not only individuals — are running it, and can be named.

### Phase 3 — Inbound

The ladder in §4 becomes the standard motion. The Diagnostic sells itself because the
tool is already trusted.

### Why the OSS launch comes after delivery evidence

An unproven project launched cold earns stars and no clients. The same project launched
behind three delivered systems earns both. Delaying the launch costs nothing and
multiplies what it is worth.

## 7. Economics

**The margin variable is human supervision time per engagement.** Not tokens, not
infrastructure — hours. Track it per engagement from the first one. If it does not fall
as the guardrail library grows, the thesis is false, and that will be visible early.

**Pricing:** anchored to client spend. Diagnostic priced low enough to be an easy yes and
high enough that free-riders self-select out. Run priced as a percentage of build,
charged monthly.

**Every engagement ships handover-ready** — documented, on the open tool, no undocumented
bespoke behaviour. This is a real per-project cost and is non-negotiable, because it is
what makes the exit promise honest and the work maintainable.

## 8. Risks and kill criteria

### Risks, ranked by likelihood of actually killing this

1. **Acquisition never starts.** EN-first, cold, no audience. The most likely failure by
   a wide margin.
2. **Supervision time does not fall.** Each engagement costs as much as the last — an
   agency with extra steps.
3. **Requirements cost dominates.** Coding collapses; elicitation and acceptance do not,
   and become the whole cost.
4. **Engine work crowds out selling.** Building is more comfortable than selling.
5. **Quality failure on an early engagement.** Small shop, warranty, no reference to
   recover with. Survivable once, not twice.

### Kill criteria — decided now, applied later

- **Phase 0.** If the engine cannot produce a working, tested, documented system from a
  scoped requirement without the bulk being hand-written, the margin thesis is unproven.
  Stop and test that before selling anything.
- **Phase 1.** If three engagements land and supervision hours do not drop between the
  first and the third, the compounding asset is not real. Reprice as a conventional
  consultancy or stop.
- **Phase 2.** If the public release produces individual users but no company adoption
  within two quarters, OSS-as-channel has failed and acquisition must come from elsewhere.
- **Any phase.** Six months of engine progress with zero paid conversations is the
  pattern that has already cost this project once.

### What working looks like

Three delivered systems, one Run contract, falling supervision hours per engagement, and
a public WisePrax with at least one named company running it in production.

## 9. Decisions and open assumptions

### Decided

| Decision | Value |
|---|---|
| Primary business | Services (delivery of outcomes), not a product |
| Structure | One org, two arms: WisePrax (platform) + WisePrax Labs (services) |
| Platform licence | Apache 2.0, **fully open** — orchestrator *and* domain guardrail/agent library |
| Platform is sold? | No. Never. It is delivery infrastructure and marketing. |
| Market | International, **EN-first** — applies from Phase 2 onward. Phase 1 engagements are opportunistic in any language or geography, because delivery evidence matters more than market fit at that stage. |
| Exit promise | Real and honoured, not a sales device |
| Brand | `Praxant` is unusable (pharmaceutical name). WisePrax = *wise* (WiseCoding) + *praxis*. |
| Funding | Services revenue. Grants remain available but are no longer the plan. Anti-VC stance unchanged. |

### Open

- **Runway and capacity are unresolved.** This design assumes enough runway for Phase 0
  and Phase 1 to complete before revenue is required. If that is false, Phase 0 must be
  cut to the minimum that supports one specific signed engagement, and Phase 1 starts
  immediately with warm contacts.
- **First vertical is unchosen.** Phase 1 requires clustering engagements in one domain
  for the guardrail library to compound. The domain can be chosen when the first client
  appears, but it must be chosen deliberately rather than accumulated by accident.

## 10. Charter amendments required

Changes to `project-specs.md`, to be made as a separate edit with a decision-log entry:

| Section | Change |
|---|---|
| §2 Audience & Positioning | Add WisePrax Labs' buyer (companies purchasing outcomes) as distinct from the platform's adopters (engineering teams) |
| §3 Strategic Intent | Record that the platform is delivery infrastructure funded by services, not a product seeking revenue |
| §7 Sub-project priority | Reprioritise per §5 above: SP-1/4/5/7 + gates first; SP-2/3 breadth, SP-8, SP-9 breadth, SP-B, SP-C deferred |
| §10 SP-D Sustainability | Replace the grant-first funding table with the §4 revenue ladder; retain grants as optional; retain all three anti-patterns |
| §11 Decision Log | Add entries for every decision in §9 above, dated 2026-08-04 |
| `ubiquitous_language.md` | Add **WisePrax Labs** |

## 11. Follow-up work (not part of this design)

- **`praxant` rename.** 205 occurrences across 56 files. The `prax-agent/` charter
  documents are already clean. Real work is confined to: the Forgejo organisation
  (`git.disposalqueen.com/praxant.ai/*`), the `praxant-ansible` skill and its references,
  the `organization_code` default, and the `.ansible-lint` author string. Local
  filesystem paths are cosmetic.
- **Diagnostic deliverable template.** The artifact sold at the first rung of the ladder
  needs a defined shape before it can be sold.
- **Engagement instrumentation.** A method for recording supervision hours per
  engagement, since it is the single metric that validates or falsifies the thesis.
