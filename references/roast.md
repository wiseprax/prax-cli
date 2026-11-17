# Roast (Shopify)

> **License:** MIT — vanilla, no carve-outs. Published as the `roast-ai` Ruby gem at `Shopify/roast`.
> **Stack:** Ruby 3.0+. Distributed as a gem and a CLI; language-agnostic in what it operates *on* (it shells out, so the target codebase can be anything).
> **Origin:** extracted from internal tooling by Shopify's **Augmented Engineering / Developer Experience** team and open-sourced.
> **Shipping status:** shipped and versioned. v0.x used YAML workflow files + Markdown/ERB prompt files; **v1.0 is a ground-up rebuild around a Ruby DSL** ("cogs"), making workflows pure Ruby — composable, expressive, testable.
> **Category:** *not a competitor.* Roast is a **workflow engine**, not an orchestration platform. It occupies the SP-1 problem space and nothing else. Closest analogue in this directory is `infrastructure-patterns.md`, but it earns its own file because it is the best published prior art for the single hardest piece of WisePrax's charter.
> **Status here:** architectural reference only. No source code is copied — not snippets, not utilities, not "just a few lines."

## What it is

Roast is a convention-over-configuration framework for **structured AI workflows**: deterministic steps and LLM steps interleaved in one declarative pipeline, run from the command line against a codebase.

The founding observation, stated plainly by the team: **"Non-determinism is the enemy of reliability."** Unrestricted agents turned loose on complex cross-codebase tasks were unreliable, so Roast decomposes the task into discrete, individually-inspectable steps and lets the author choose, per step, whether that step is an LLM call or ordinary code.

The v1.0 primitives are **cogs**, declared inside an `execute` block:

| Cog | Does |
|---|---|
| `chat` | cloud LLM call (OpenAI, Anthropic, Perplexity, Gemini) |
| `agent` | **runs a local coding-agent CLI with filesystem access** (Claude Code CLI, Pi CLI) |
| `ruby` | arbitrary Ruby |
| `cmd` | shell command |
| `map` | fan out over a collection, serial or parallel |
| `repeat` | iterate |
| `call` | invoke a reusable workflow scope |

Output flows forward between cogs and is read back by name (`cog_name!()`).

Two properties matter more than the cog list:

1. **Session replay.** Execution state is saved automatically, so a workflow can resume from any step without re-running the expensive LLM steps before it. The team frames this primarily as a *development-speed* feature — you iterate on step 7 without paying for steps 1–6 again — not just as crash recovery.
2. **Progressive determinism.** The intended authoring path is: prototype a step as an LLM placeholder, understand the problem, then **replace that step with deterministic code**. The framework is designed so the AI content of a workflow *shrinks over time*. Shopify's own examples pair a deterministic cleanup pass with an `agent` cog that handles only the residue the deterministic pass couldn't.

Reported internal uses: test grading across thousands of files, automated Sorbet type-annotation ("Boba"), SRE signal-scanning over Slack, competitive-intelligence aggregation, commit-history archaeology.

## Where it differs in shape

Roast is a **single-user CLI library**. It has no server, no queue, no multi-tenancy, no persistence service, no supervision channel, no isolation story, no identity model, and no task lifecycle. It is one layer of WisePrax, not a rival to it.

| Dimension | Roast | WisePrax |
|---|---|---|
| Shape | Ruby gem + CLI, invoked by a developer | served Go orchestrator, background/queued |
| Workflow definition lives in | **a Ruby file in the repo, under version control** | **Postgres, authored from the admin UI** (`CLAUDE.md`, SP-1) |
| Human posture | fire-and-inspect; resume by replay | supervising a running agent, interruptible mid-task (SP-6) |
| Isolation | none — runs on the host filesystem | Docker container + default-deny egress |
| Task state | local session files | the user's VCS (Forgejo v1) |
| Multi-user | ✗ | ✓ (Keycloak/OIDC) |
| Runtime wrapping | `agent` cog shells to Claude Code / Pi CLI | SP-4 adapter contract |

## The charter tension this raises

**This is the reason the file exists.** `CLAUDE.md` records a locked decision:

> **Configurable workflow engine:** workflow definitions (states / transitions / actor types / conditions / task dependencies) live in Postgres and are authored from the admin UI — **no config-file editing.**

Roast is the strongest published counter-evidence to that decision, and the direction of its own evolution is the sharp part:

- v0.x was **YAML + Markdown** — declarative, data-shaped, exactly the kind of definition that stores cleanly in a database row.
- v1.0 threw that out and moved to a **Ruby DSL**. Shopify moved *toward* code, not away from it, after real production use.

The forces that pull that way are the ones WisePrax will meet too: workflow authors want conditionals, loops, and reusable scopes; they want to unit-test a workflow; they want workflows reviewed in a pull request alongside the code they operate on; and they want `git blame` on a transition that started misbehaving.

This does **not** automatically invalidate the charter decision. WisePrax's requirements genuinely differ:

- Team-wide enforcement — the four-part test in `project-specs.md` §4 puts *team-enforced* configuration in the platform, and a file in one repo cannot enforce across many.
- Non-developer authorship from an admin UI is an explicit goal; a Ruby DSL is not.
- Deterministic execution against a *fixed* definition requires a versioned, immutable snapshot at run start — which is a database problem, not a file problem.

But the tension is real and should be recorded rather than smoothed over. The likely synthesis, and the honest recommendation:

**Definitions remain DB-authoritative and DB-versioned (charter unchanged), plus a lossless export/import to a text representation, so a definition can be diffed, code-reviewed in Forgejo, and restored.** That buys `git blame` and PR review without giving up team-wide enforcement, UI authorship, or run-time snapshot immutability. Charter-compatible; needs a §11 entry.

## Patterns worth borrowing (without code)

### 1. Step-level resumability as the default, not a recovery path

Roast checkpoints between steps so a re-run skips completed LLM work. **Adopt for SP-1.** WisePrax already needs a durable state machine; making each transition a resume point turns "the container died at 03:00" from a lost run into a continuation, and — the underrated half — turns *workflow authoring* from a costly loop into a cheap one. A workflow engine you cannot iterate on cheaply will not get iterated on.

### 2. Progressive determinism as an explicit design goal

The stated intent that LLM steps get **replaced by deterministic code as understanding matures** is the correct posture for a supervision platform and belongs in doctrine, not just in tooling. **Adopt as a stated principle.** It is the operational form of the substrate-not-behavior tenet: the platform should make it easy for a team to *shrink* the non-deterministic surface of a workflow over time, and should make the current size of that surface visible.

### 3. First-class step typing, with agent-CLI invocation as one type among many

Roast does not treat "call an LLM" as the only interesting step. `cmd`, `ruby`, `map`, `repeat`, `call` are peers of `chat` and `agent`. **Adopt for SP-1's actor types.** `CLAUDE.md` already says "skills act as actors via REST or MCP (interface TBD)" — Roast's cog taxonomy is a worked-out answer to what that actor-type enum should contain: at minimum *agent-runtime*, *skill/REST*, *shell*, *fan-out*, *loop*, *sub-workflow*. The `call` cog in particular argues that **sub-workflow-as-actor** should be in v1 of the enum, not deferred.

### 4. `map` with a serial/parallel switch as the fan-out primitive

One primitive covers both "review these 40 files one at a time" and "ask four models simultaneously." **Adopt for SP-7.** The council does not need a bespoke mechanism; it is `map` over reviewers with parallel semantics, a join, and a delivery-denial rule for round 1 (see `traycer.md` §2). Building the council on a general fan-out primitive rather than a special case keeps it inside the substrate boundary.

### 5. Repo-local prompt/context files, engine-agnostic

Roast reads prompts from Markdown files in the repo with ERB context injection. Same family as Traycer's `AGENTS.md` auto-detection and Open SWE's `AGENTS.md` convention. **Adopt** — consistent with substrate-not-behavior: prompts are repo-authored behavior; the platform only supplies plumbing and context.

## Patterns to adapt, not copy

- **Workflow-as-code.** See the tension section above. Adapt to export/import over a DB-authoritative definition; do not adopt file-as-source-of-truth.
- **Host-filesystem execution.** Roast's `agent` cog runs a coding CLI directly against the developer's filesystem with no isolation. WisePrax needs the same capability inside a container with default-deny egress. Take the *step type*, not the *execution location*.
- **Shared cumulative transcript across steps.** Roast lets later steps read earlier transcripts. Useful, but WisePrax must scope it: SP-7 round 1 requires reviewers *not* to see each other. Blind review is a transcript-visibility rule, so transcript sharing has to be per-edge policy, not a global default.

## Patterns to reject

- **No isolation boundary.** Non-negotiable for WisePrax; container + egress allowlist stays.
- **Provider credentials from environment variables.** Direct conflict with SP-9's short-lived per-task OpenBao tokens.
- **CLI-only, single-user surface.** Roast has no reason to have identity, queueing, or a control surface. WisePrax's entire value is at team scale.
- **Cloud-LLM-first `chat` cog.** Not wrong for Roast, but WisePrax's cost shape is subscription-billed CLI runtimes (§4.1), not per-token API calls. The `agent` cog is the model to follow, not `chat`.

## Why WisePrax is still different

Roast does not compete on any of the eight axes except workflow structuring, and even there only for a single developer on a single machine. It has no position on VCS-of-truth (§4.2), council (§4.3), live supervision (§4.4), secrets (§4.5), or container egress (§4.7).

Where it *is* ahead, and where WisePrax should be humble: **on the substrate-not-behavior tenet (§4.6), Roast is arguably purer than WisePrax's own design.** It ships a workflow engine and nothing else — no modes, no opinions about code quality, no bundled review policy. Every WisePrax feature proposal should be asked whether it would survive in Roast's scope. Most would not.

## Where Roast is ahead today

- It is running in production at Shopify scale, on real problems (test grading, type-annotation migrations), not in a spec.
- Session replay is working software; SP-1 has resumability as an intention.
- Two full generations of workflow-definition design have been through production, and the second was informed by the first. WisePrax has one un-built generation.
- Its step taxonomy is more complete than SP-1's currently-undefined actor-type enum.

## Cross-reference

- `COMPARISON.md` §2 (matrix row), §3 (placement), §4.6 (substrate boundary — Roast is the purity benchmark).
- `open-swe.md` — the other 2026 entrant, and the opposite architectural bet (cloud sandboxes, own agent loop).
- `traycer.md` §2 — capability decomposition, needed to make Roast's shared-transcript pattern safe for blind council review.
- `project-specs.md` §4 (four-part test), SP-1, SP-7.

## Open items for the charter

These change or add to decided design and need `project-specs.md` §11 entries when adopted:

1. **Lossless export/import of workflow definitions to a reviewable text form**, keeping Postgres authoritative. Resolves the workflow-as-code tension without reversing the charter decision.
2. **Step-level resumability** as an SP-1 requirement, justified for authoring speed as well as fault recovery.
3. **Actor-type enum for SP-1**, seeded from Roast's cog taxonomy, with **sub-workflow-as-actor included in v1**.
4. **`map`-style fan-out as the general primitive underlying SP-7**, rather than a purpose-built council mechanism.
5. **Progressive determinism** adopted as stated doctrine, with the non-deterministic surface of a workflow made visible in the control surface.
