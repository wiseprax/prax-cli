# WisePrax v0.1 Persona Catalog — Proposed Adjustments

*Working note. WisePrax AI team. For team review.*

## Purpose

WisePrax's v0.1 persona catalog (the catalog of who can act on a task) starts from the role-based shape the team is already running with: **BA, PM, Architect, Developer, QA, Technical Writer**. That catalog works — we've validated it by use. Before we lock it into WisePrax's admin UI and workflow engine, this note proposes a small number of adjustments to handle WisePrax's specific architectural commitments (SP-6 live supervision, SP-7 multi-model review council, SP-1 configurable workflow).

Adjustments are intentionally minimal: one rename, one split, two additions, one metadata-level tagging. Existing skills mostly carry over unchanged.

## What stays (the foundation)

- **The role-based shape itself.** SDLC roles (BA, PM, Architect, Developer, QA, Technical Writer) map cleanly onto how teams already think about work. Easier to teach, easier to onboard, fits existing PR / issue conventions.
- **The principle that personas are team-configured.** WisePrax's substrate-not-behavior tenet means the catalog below is a v0.1 *default* — every team using WisePrax can adjust it in the admin UI. We're shipping a working starting point, not a rigid contract.
- **Existing skill content.** Prompts, instructions, tools, and behavior inside current skills don't need to change. This is a catalog adjustment, not a skill rewrite.

## What needs adjusting (and why)

### 1. Add **Reviewer** as a distinct persona, separate from QA

**Why:** SP-7 commits WisePrax to a multi-model review council — round 1 blind/parallel, round 2+ sighted debate, hard cap 3 rounds. The council members evaluate *code judgment and design soundness* (is this change well-architected? does it introduce risk?). That's a different kind of work from *execution validation* (do the tests pass? does the feature behave correctly?).

In the current catalog these collapse into "QA." For SP-7 to work, **Reviewer needs to exist independently of QA**, and needs to be N-instance-able (Reviewer-Claude, Reviewer-GPT, Reviewer-Gemini) so the council has provider diversity.

- **Reviewer** — code/design judgment. Council member. Multiple instances of different model providers.
- **QA** — execution validation against the task's validation contract. Single instance is fine.

### 2. Refactor **PM** — it's a team role, not a per-task actor

**Why:** PM in a normal software org coordinates *across* tasks, manages stakeholders, balances roadmap. None of that is per-task agent work. When PM is encoded as a per-task persona, it produces awkward agent outputs ("PM-agent" doesn't write code, doesn't review, doesn't test — it tries to invent a deliverable to justify its slot in the workflow).

Proposed split:

- **Coordinator** (new, agent persona) — dispatch routing, escalation routing, cross-actor awareness inside a single task's lifecycle. Has real agent-shaped work: decides which praxagent runs next, applies escalation policy when blocked, writes the cross-actor handoff summary. *Name choice:* "Orchestrator" was the obvious alternative but is already the name of the platform spine (the Go process — see `CLAUDE.md` and `project-specs.md`). Reusing it as a per-task persona would force readers to disambiguate "the orchestrator" (platform) from "an Orchestrator" (an actor on this task) on every mention. Coordinator avoids the collision and reads honestly: it's per-task glue, not a platform-level concept.
- **PM stays as a team-level human role** outside the per-task workflow — manages roadmap, talks to stakeholders, lives in the kanban macro view (project / milestone / cross-task), not the task drill-down.

The person playing PM in the team is still important. They just don't fire per-task workflow transitions.

### 3. Add **Operator / Supervisor** as a first-class human persona

**Why:** SP-6 commits WisePrax to a live mid-task message bus — humans can talk to running agents without aborting them. The human on the other end is an **operator**, not the PM. They:

- Answer questions agents ask mid-task (via the MCP human-input tool)
- Apply kanban labels for control (`praxant:pause`, `praxant:escalate-council`, `praxant:abort`, …)
- Watch token / wall-clock budget burn
- Intervene when an agent is stuck

This persona is missing from the current catalog. Without it, "who is the human at the keyboard when an agent asks a clarifying question mid-task?" has no clean answer — it gets pushed onto whoever happens to be watching, with no defined authority or accountability.

### 4. Add **Approver** as a distinct authorization persona

**Why:** "Who may fire the `merge` or `promote` transition?" is a policy question WisePrax's configurable automation policy explicitly needs to answer. In the current catalog it's implicit — probably whoever happens to be playing PM. For real teams, merge authority and project-management authority are different roles: a release manager isn't always a PM, a senior architect with merge rights isn't always the PM, etc.

Approver is a named persona for the team to wire to whoever holds merge / promote authority for a given project. Often distinct from PM.

### 5. Tag each persona as `agent`, `human`, or `either`

**Why:** WisePrax dispatch differs based on persona type. Agent personas run inside a praxagent container with a Ralph Loop and message bus. Human personas get reached via chat approval requests, kanban labels, or the MCP human-input bus. The current catalog leaves this implicit; making it explicit metadata removes ambiguity at dispatch time.

This isn't a new persona — it's a one-field tag on the ones we have:

| Persona | Default tag |
|---|---|
| BA | either |
| Architect | either |
| Developer | agent |
| Reviewer | agent |
| QA | agent |
| Technical Writer | either |
| Coordinator | agent |
| Operator | human |
| Approver | human |

Teams can override the defaults in the admin UI.

### 6. Make every persona N-instance-able

**Why:** SP-7 needs multiple Reviewer instances. But the same shape applies elsewhere — redundant QA, parallel exploration by two Developer instances, etc. The catalog should let the team configure N concrete actor instances per persona type, each with its own model / provider / runtime.

Operationally: "Reviewer" is a persona *type*; the team configures three concrete *instances* (Reviewer-Claude, Reviewer-GPT, Reviewer-Gemini) in the admin UI; workflow transitions that require "a reviewer" are policy-configurable to require 1, majority, or all instances to agree.

## Proposed v0.1 default catalog

| Persona | Tag | Per-task work |
|---|---|---|
| BA | either | Requirements analysis, scope clarification |
| Architect | either | Design proposal, ADR drafting, system-level decisions |
| Developer | agent | Implementation, debugging, refactoring |
| Reviewer | agent (N) | Code judgment, design review, SP-7 council |
| QA | agent | Test execution, validation contract enforcement |
| Technical Writer | either | Documentation, handoff reports |
| Coordinator | agent | Dispatch routing, escalation, cross-actor handoff summary |
| Operator | human | Mid-task supervision, kanban control, MCP human-input |
| Approver | human | Merge / promote authorization |

Removed from per-task catalog: **PM** (stays as a team-level human role outside the task workflow).

## How existing skills migrate

Most skills carry over without change:

| Current actor | Becomes | Skill change needed? |
|---|---|---|
| BA | BA | No |
| PM | Coordinator (rename + scope narrow) | Rename only; behavior is already coordinator-shaped |
| Architect | Architect | No |
| Developer | Developer | No |
| QA | QA + Reviewer (split) | QA skills keep execution-validation work; Reviewer is a new skill set for code/design judgment |
| Technical Writer | Technical Writer | No |

Net: **one rename, one split, two additions** (Operator, Approver). All other content stays.

## What this is not

- **Not a rejection of the current shape.** The role-based foundation stays. We're refining edges, not redesigning the catalog.
- **Not a critique of past work.** The current catalog is what made it possible to validate the workflow shape at all. The adjustments are about fitting it to WisePrax's architectural commitments (council, supervision, merge policy), which weren't in scope when the original catalog was authored.
- **Not a permanent shape.** This is the v0.1 *default*. Substrate-not-behavior says teams adjust in the admin UI. We're shipping a starting point.

## Open questions for the discussion

1. **BA vs. Architect for v0.1.** Worth folding into one persona for v0.1 (both do up-front analysis/design) and splitting later? Or keep distinct from the start?
2. **Approver granularity.** One Approver persona, or several (merge-approver, release-approver, security-approver)?
3. **Council shape.** Always three Reviewer instances, or configurable per-workflow?
4. **Multi-instance default vs. opt-in.** Should every persona default to single-instance with multi-instance opt-in, or are some personas (Reviewer especially) multi-instance by design?

---

*If we agree on the shape, I'll capture the v0.1 default catalog in `project-specs.md` SP-8 detail and `ubiquitous_language.md`, replacing the current persona-related entries. Open questions stay open until we settle them in a follow-up.*
