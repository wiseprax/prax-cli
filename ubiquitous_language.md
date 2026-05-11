# Praxant — Ubiquitous Language

**Status:** Living document
**Date:** 2026-04-28

---

## What This Is and How to Use It

This document is the canonical vocabulary for Praxant. It captures the precise meaning of every term used in the project — across documentation, code, commit messages, PR comments, chat conversations with the team, conversations with AI agents (Claude, OpenCode, etc.), grant applications, and marketing copy.

The concept comes from **Domain-Driven Design** (Eric Evans) — the *ubiquitous language* is the shared vocabulary that developers, domain experts, and (now) AI agents all use the same way. When everyone uses the same words to mean the same things, miscommunication collapses, planning becomes precise, and AI-generated code aligns with intent without requiring constant re-explanation.

**How to use this file in practice:**

| When | What to do |
|---|---|
| Working with an AI agent on Praxant code or docs | Have this file open in a tab; reference terms by their canonical name |
| Writing new docs or specs | Use the canonical terms; if a needed concept isn't here, add it to this file first, then use it |
| Code review | Flag PRs that introduce new terms without defining them here, or that misuse existing terms |
| Onboarding a new contributor | Send them this file as the second thing to read after the README |
| Adding a new abstraction or component | Add the term to this file *before* writing the code |

**What this document is NOT:**

- A glossary of every word the project uses (it's the *deliberate* vocabulary, not a dictionary)
- A place to introduce aspirational concepts (only terms that are actually used)
- A substitute for the project charter (`project-specs.md`) — that file says *what we're building*; this file says *what we call the things we're building*

---

## 1. Project Identity

| Term | Definition | Usage notes |
|---|---|---|
| **Praxant AI** | The umbrella organization behind the project. The team, the brand entity, the GitHub org (`praxant-ai`), and the canonical owner of the domain `praxant.ai`. Distinct from Praxant (the platform). | Use when referring to the team, the entity, governance, brand, or external positioning ("Praxant AI team", "Praxant AI is the umbrella for…"). |
| **Praxant** | The platform / project / orchestration system. Coined from Greek *praxis* (action with practical wisdom) + Latin *-ant* (one who does). Pronunciation: PRAK-sant. | Use when referring to the platform itself — the Go orchestrator, the workflow engine, the kanban app, the adapters. The product Praxant AI builds. |
| **Praxagent** | A single agent instance running under Praxant supervision — a containerized worker with the full Praxant wrapper (Ralph Loop with caps, message bus integration, council eligibility, structured output capture, role config). | Use when referring to a specific running agent OR when emphasizing the difference from a raw agent CLI session. The plural is "praxagents." |
| **praxant.ai** | Canonical domain. Not an identity term — just an address. | Use as URL only. Do not use "praxant.ai" to refer to the team or the platform; use "Praxant AI" and "Praxant" respectively. |

---

## 2. Agent Concepts

| Term | Definition | Usage notes |
|---|---|---|
| **Agent CLI** | The underlying command-line tool that drives an AI model to do coding work. Examples: `claude`, `codex`, `opencode`, `gemini`, `cursor-agent`. | Praxant orchestrates agent CLIs by spawning them in containers. Use this term when discussing the underlying tool, not the wrapper. |
| **Agent runtime** | A specific agent CLI configured for use within Praxant — including its model, harness version, and integration adapter. | Equivalent to "agent CLI" most of the time; use "agent runtime" when emphasizing Praxant's abstraction over multiple CLIs (SP-4). |
| **Role** | A configured assembly of: skills + system prompt + default model + container image + tool/path permissions + active hooks. Each role represents a specialist (e.g., backend-developer, frontend-developer, qa, pm, odoo-developer). | Use when describing agent specialization. A *role* is persistent and reusable across many tasks; an individual run of a role on a task is a *praxagent invocation*. |
| **Skill** | A reusable unit of procedural knowledge encoded as a markdown file with frontmatter. Examples: `python-core`, `python-odoo`, `deploy-checklist`, `wp-coding-standards`. Skills are loaded into a praxagent's context based on its role's configuration. | Skills are *building blocks*. Roles *compose* skills. Use "skill" for the markdown file/concept; use "skill-loaded" or "active skill" when describing runtime behavior. |
| **Hook** | A gating or validation mechanism that runs before/after specific events in a praxagent's lifecycle (e.g., before a git operation, after a file write, before a tool call). Hooks enforce policy — e.g., "block any commit directly to main." | Hooks are imperative (they execute and can fail); skills are declarative (they provide knowledge). Use the right word — hooks ≠ skills. |
| **System prompt** | The role-specific instructions loaded into the praxagent at the start of a task. Defines the agent's persona, responsibilities, and constraints. Lives in `prompts/<role>.md`. | Use when discussing the agent's "instructions" or "persona." Don't confuse with task-specific prompts. |
| **Tool surface** | The set of tools (file edit, git commit, shell exec, MCP servers, etc.) that a specific role's praxagent is permitted to use. Defined in the role config. | Use when discussing per-role permissions. Smaller tool surface = safer, more focused praxagent. |

---

## 3. Workflow & Lifecycle

| Term | Definition | Usage notes |
|---|---|---|
| **Task** | A single unit of work routed to one praxagent. Each task corresponds to one container, one role, one model, one execution. Atomic. | Use "task" for the unit of orchestrator dispatch. A single user request might span multiple tasks (one per phase). |
| **Phase** | A named stage in a multi-step workflow. Standard phases: `analyze`, `implement`, `testing`, `documenting`, `reviewing`, `ready-for-merge`. Phase is tracked via VCS labels (e.g., `phase/implement`) on the issue or PR. | Use "phase" when discussing what stage a task is in. Phase transitions trigger new task dispatches to the appropriate role. |
| **Phase transition** | A label change on an issue or PR that signals the workflow has advanced to the next stage. Picked up by the orchestrator's label-driven dispatcher. | The state machine of a workflow IS the sequence of phase transitions. There is no separate workflow engine — labels and the dispatcher are the workflow. |
| **Phase-boundary skill** | A skill that constrains what a praxagent can do during a specific phase. Examples: `python-testing-phase` (constrains agent output to tests/ only), `documenting-phase` (constrains to docs/ only). Loaded into role context based on the current phase label. | Use when discussing how phases scope agent capabilities. Distinct from regular skills which provide knowledge without constraining output. |
| **Handoff report** | A structured artifact (typically a markdown comment on the PR) that a praxagent writes at the end of its phase. Documents what was done, what's still open, what assumptions were made, and what the next phase needs. | Use when discussing inter-agent communication. The handoff report is the canonical "this is what I did" record — both for the next praxagent and for human reviewers. |
| **Workflow** | An emergent sequence of phase-transitions for a single feature or issue. Not a separate engine — the workflow is just the chronological sequence of phases the issue moves through, tracked by VCS labels. | Use sparingly; "workflow" is descriptive (what happened), not architectural (no workflow code exists). Prefer "phase sequence" or "lifecycle" when being technical. |
| **Automation policy** | The configurable rules that define what a praxagent or workflow is allowed to do automatically. Examples: whether a task needs human approval before dispatch, whether a PR may be auto-opened, whether a merge may happen after consensus and checks pass. | Use when discussing what agents are authorized to automate. This is a first-class Praxant concept. |
| **Review policy** | The configurable rules that determine how review happens for a task or PR: required reviewers, whether a council is used, what quality gates must pass, and when escalation to a human is required. | Use when discussing the structure of review rather than the mechanics of one specific reviewer. |
| **Merge policy** | The configurable rules that determine when a PR may be merged: human approval only, policy-authorized after checks, or blocked pending remediation. | Use when discussing merge authority. Avoid implying that humans always merge or that auto-merge is always enabled. |
| **Quality gate** | A deterministic check or threshold that must pass before the workflow can advance. Examples: tests, coverage, complexity, security scan results, lint, policy checks. | Use when discussing enforcement criteria. Quality gates are evaluated by code, not by vibe. |
| **Escalation rule** | A deterministic rule that forces the workflow to stop or request human involvement when a condition is met: disagreement, risk threshold, failed checks, sensitive paths, etc. | Use when discussing when autonomy stops and a human or higher-trust review is required. |

---

## 4. Multi-Model Review Council

| Term | Definition | Usage notes |
|---|---|---|
| **Council** | The pattern of dispatching multiple reviewer praxagents (each with a different model) against the same PR to reach consensus or surface dissent. | Use only when describing the *review* pattern. Do NOT use "council" for any author-side pattern — review and authorship are distinct. |
| **Reviewer** | A praxagent specifically configured to review a PR rather than author code. Reviewer roles have their own bot accounts in the VCS (e.g., `bot-reviewer-claude`, `bot-reviewer-deepseek`, `bot-reviewer-qwen`) and their own posting permissions. | Use to distinguish from author. A reviewer praxagent never modifies code; it only posts review state and comments. |
| **Round** | One pass of the review council. Round 1 is parallel and blind; subsequent rounds are sighted. Hard cap of 3 rounds before falling back to human resolution. | Use "round" for council iterations specifically. Don't conflate with Ralph Loop iterations (which are within a single praxagent). |
| **Blind round** | A round where reviewer praxagents do not see each other's prior comments before posting. Always Round 1. Designed to maximize independence of failure modes. | Critical concept — without blindness, reviewers converge on consensus too quickly (LLM sycophancy). |
| **Sighted round** | A round where reviewer praxagents can read each other's prior comments. Round 2+ if Round 1 produced mixed results. | Allows refinement and addresses concerns one reviewer raised that others missed. |
| **Consensus** | All reviewers in the final round arrived at the same review state (`APPROVED`). PR is labeled `agents/consensus`. | Consensus is an input to policy. Depending on merge policy, it may authorize merge, authorize the next stage, or still require human approval. |
| **Dissent** | The final round produced mixed reviewer states. PR is labeled `agents/dissent` with a summary of the disagreement. Surfaces to a human for resolution. | Dissent is informative, not failure. The system explicitly accepts that disagreement requires human judgment. |
| **Aggregator** | The orchestrator component that watches reviewer review states across rounds and decides when consensus/dissent is reached. Applies the appropriate label and posts the summary. | A small piece of code (~50 LOC), not a separate sub-project. |

---

## 5. Provider Abstractions

Praxant has six provider abstractions, each with a default v1 adapter and an open extension point.

| Abstraction | Day-one adapter | Concept |
|---|---|---|
| **AgentRuntime** | Claude Code (subscription via OAuth token) | Interface for spawning agent CLIs as subprocesses |
| **ModelRouter** | (Implicit — agent CLIs handle their own model auth) | Future abstraction for direct model routing if needed |
| **VCSProvider** | Forgejo | Webhook ingest, posting comments/reviews/labels, PR operations |
| **ChatProvider** | Mattermost | Posting messages, reactions (approval primitive), threads, supervision messages |
| **SecretProvider** | OpenBao (with `.env` file as zero-config fallback) | Reading secrets, writing secrets (for OAuth refresh), per-task injection into containers |
| **Container runtime** | Docker | Spawning per-task containers, tmpfs mounts, network isolation |

| Term | Definition | Usage notes |
|---|---|---|
| **Provider** | An abstraction over a class of external system (VCS, chat, agent runtime, etc.). Defines the interface Praxant codes against. | Use "provider" for the interface; use "adapter" for the concrete implementation. |
| **Adapter** | A concrete implementation of a provider interface. E.g., `ForgejoAdapter` is an adapter for the `VCSProvider`. | Each provider has at least one adapter. Adapters are the extension point — adding support for GitHub means writing a `GitHubAdapter`. |
| **Vendor-neutral** | Property of an architecture where every external dependency is behind an abstraction. Praxant is vendor-neutral across all six provider categories. | Use when describing the strategic positioning. Antonym: vendor-locked. |
| **Sovereign** | Property of a deployment where all data and compute remain under the user's control (their hardware, their VCS, their chat, their model provider, their secrets). No third-party SaaS dependencies. | Praxant is positioned as sovereign-friendly. Use when describing the audience or value proposition. |

---

## 6. Supervision & Messaging

| Term | Definition | Usage notes |
|---|---|---|
| **Approval gate** | A human-in-loop checkpoint before a praxagent is dispatched. The orchestrator posts a message to the configured chat platform; an authorized user must react with ✅ before execution begins. | Implemented via the universal "emoji reaction" primitive — works identically across Mattermost, Slack, Rocket.Chat, Discord. |
| **Message bus** | The per-task bidirectional channel between humans and a running praxagent. Implemented via PostgreSQL `LISTEN`/`NOTIFY` on a channel named `task_<id>_messages`. | Use when discussing live supervision. The message bus is for runtime communication, not approval (which is a separate primitive). |
| **Operator** | A human user of Praxant who dispatches, supervises, approves, or interacts with praxagents. | Use "operator" instead of "user" when emphasizing the supervisory role. Operators talk to praxagents via the message bus or the Kanban UI. |
| **MCP human-input tool** | An MCP server tool exposed to praxagents that lets the agent ask the operator for input mid-execution. Posts to the configured chat thread, blocks for response, returns the operator's reply. | Distinct from the message bus (which is operator-initiated). MCP human-input is agent-initiated. |
| **Live supervision** | The combined capability of: (1) watching praxagent progress in real time via Kanban UI, (2) sending messages to a running praxagent via message bus, (3) being asked for input by the praxagent via MCP human-input tool, (4) inspecting the running container directly via shell. | One of Praxant's core differentiators. Use when contrasting with fire-and-forget CI-pipeline approaches. |
| **Drill-down** | The Kanban card detail pane that shows live progress, log stream, file changes, message thread, and shell access for a specific running task. | Use when discussing the UI for inspecting a single task. |

---

## 7. Runtime & Deployment

| Term | Definition | Usage notes |
|---|---|---|
| **Single-host Docker** | Praxant's deployment model: one host (developer workstation, mini-PC, homelab box, dedicated server) running Docker. No Kubernetes, no service mesh, no distributed orchestration. | Use when describing the deployment philosophy. Non-negotiable v1 design choice. |
| **Orchestrator** | The long-running Praxant daemon. Responsibilities: webhook ingest, task queue, dispatcher, concurrency control, container management, message bus pub/sub, label-driven phase routing, WebSocket UI server. | Use "orchestrator" for the daemon process. Distinct from the Praxant CLI. |
| **Per-task container** | An ephemeral Docker container spawned by the orchestrator for one specific task. Contains the agent runtime CLI + Ralph Loop entrypoint + role-specific tooling. Destroyed when the task completes. | Use when describing isolation or resource limits. Contrast with long-running services (Postgres, OpenBao, orchestrator itself). |
| **Container image** | A Docker image used to spawn per-task containers. Praxant uses a layered approach: a single fat base image (`praxant/agent-base`) with all CLIs, plus per-stack overlays (`agent-base-odoo`, `agent-base-wp`, etc.). | Use when discussing the image strategy. Per-stack overlays are added on demand. |
| **Ralph Loop** | The iterative pattern where a praxagent runs a goal-driven prompt repeatedly, checking for completion after each iteration, with hard caps on iterations / tokens / wall-clock to prevent runaway. Named after the original "Ralph Wiggum loop" pattern. | Use when discussing the praxagent execution loop. Don't confuse with workflow phases (which are between tasks, not within one). |
| **Iteration cap** | Maximum number of Ralph Loop iterations before a praxagent exits with a "deferred — needs human" status. Default: 50. | Use when discussing safety bounds within a task. |
| **Concurrency cap** | Maximum number of praxagents running in parallel across the entire orchestrator. Default: 3 (sized for Anthropic Max 20×). | Use when discussing system-level limits. Distinct from iteration cap (per-task). |
| **Tmpfs-mounted secret** | A secret value delivered to a per-task container as a file in `/run/secrets/<name>`, mounted on tmpfs (RAM-backed, never written to disk), with mode 0400. The modern industry standard for container secrets — preferred over environment variables. | Use when discussing secret delivery. Always prefer over env-var delivery. |
| **Default-deny egress** | The container network policy that blocks all outbound network connections except to an explicit allowlist (Anthropic API, Forgejo, package mirrors). | Use when discussing container security. The single highest-leverage security control for autonomous agent execution. |
| **Subscription auth** | Authentication via a long-lived OAuth token (e.g., `CLAUDE_CODE_OAUTH_TOKEN`, valid 1 year) that bills usage against a personal/team subscription rather than per-token API billing. | Use when discussing how Praxant connects to AI providers. Subscription auth is core to Praxant's cost model. |
| **Quota-aware backoff** | The dispatcher behavior of pausing new task dispatches when a provider returns rate-limit responses (HTTP 429). Resumes when the rate-limit window opens. | Reactive, not predictive. Use when discussing how Praxant respects provider rate limits. |

---

## 8. Trust & Threat Model

| Term | Definition | Usage notes |
|---|---|---|
| **Developer-workstation threat model** | Praxant's foundational security stance: protect against realistic threats to a developer's own machine (backups, snapshots, file leaks, screenshots, casual logs) — not against enterprise threats (datacenter compromise, multi-party authorization, regulated audit). See `THREAT_MODEL.md`. | Use when justifying security design choices. The single most important architectural principle for security decisions. |
| **Trust boundary** | The single host where Praxant runs. Everything inside the host is treated as trusted; everything outside is untrusted. | Use when discussing what Praxant defends against vs what it doesn't. |
| **Auto-unseal** | OpenBao's behavior of automatically unsealing on host startup using a key stored on the same host. Praxant's deliberate choice — manual unseal would be friction without realistic benefit at this threat model. | Use when explaining the OpenBao deployment model. Anticipate pushback from enterprise-mindset contributors. |
| **Skeptical empiricism** | Praxant's design stance: prefer what empirically works today; treat hype with suspicion; require demonstrable results before claiming capabilities. | Use when discussing project values and how to evaluate proposed features or model recommendations. |

---

## 9. Anti-Vocabulary (Terms We Explicitly Avoid)

These terms imply assumptions that contradict Praxant's design. Use the suggested alternatives instead.

| Avoid | Use instead | Why |
|---|---|---|
| **Sidecar** | Co-located service / separate container in the same Docker network | "Sidecar" is Kubernetes vocabulary that implies pods. Praxant doesn't use Kubernetes. |
| **Pod** | (no equivalent — we don't group containers) | Same — K8s vocabulary. We have containers, not pods. |
| **Helm chart** | Docker Compose file | Same — K8s vocabulary. |
| **Operator** (in the K8s sense — a controller process) | Daemon, systemd service | "Operator" in Praxant = the human user; do not overload. |
| **Service mesh** | Docker network | Same — K8s vocabulary; overkill for single-host. |
| **Init container** | Entrypoint script, pre-flight container | K8s vocabulary. |
| **My-** prefix on naming | (use distinctive coined names instead) | Dated 2003-era pattern. Generic, weak retrieval, dilutes brand. |
| **Slaves** (for agents) | Praxagents, workers, agents | Bad branding regardless of intent; primes "tool" framing where "collaborator" framing produces better behavior. |
| **Defense in depth** (without specific threat) | (cite a specific realistic threat from §3 of THREAT_MODEL.md) | Vague justification that often hides security theater. Always require a concrete threat. |
| **Hardening** (as a goal) | (cite a specific realistic threat from §3 of THREAT_MODEL.md) | Same — "hardening" without naming the threat is performative. |
| **Vibe coding** | (this is what we are NOT — see project-specs.md) | Praxant is built on the premise that code quality and software fundamentals matter. We are the opposite of vibe-coding. |
| **AI workforce, autonomous engineers, agents that ship features end-to-end** | (be empirical and specific) | Hype language; Praxant's marketing is honest about what works and what doesn't. |
| **Cheap planner + frontier executor** (for author-side splitting) | Single agent per task; specialization across tasks via roles | The pattern empirically loses to a solo strong model in a mature harness for greenfield tasks. We use role specialization across tasks instead. |
| **Author orchestration** (without distinguishing the type) | Specify: tier-based author splitting (rejected) vs role-based author specialization across tasks (default) | Two very different patterns; lumping them together causes confusion. |
| **Enterprise tier, Pro tier** (for Praxant features) | (don't pre-build paid tiers without real demand) | Open-core temptation that hurts community trust. Praxant is OSS first. |

---

## 10. Naming Conventions

| Surface | Convention | Example |
|---|---|---|
| GitHub org | `praxant-ai` | `github.com/praxant-ai` |
| Repo (v1 product) | `praxagent` | `github.com/praxant-ai/praxagent` |
| CLI binary | `praxant` | `praxant init`, `praxant secrets list`, `praxant auth refresh claude` |
| Daemon process | `praxant-orchestrator` | systemd unit name |
| Docker images | `praxant/<purpose>` namespace | `praxant/agent-base`, `praxant/orchestrator` |
| VCS labels (phase tracking) | `phase/<name>` | `phase/analyze`, `phase/implement`, `phase/testing` |
| VCS labels (council outcomes) | `agents/<state>` | `agents/consensus`, `agents/dissent` |
| VCS labels (role routing) | `agent/<role>` | `agent/backend-developer`, `agent/qa` |
| Reviewer bot accounts | `bot-reviewer-<model>` | `bot-reviewer-claude`, `bot-reviewer-deepseek` |
| Skill files | `skills/<skill-name>.md` | `skills/python-core.md`, `skills/python-testing-phase.md` |
| Role files | `agents/<role-name>.yaml` | `agents/backend-developer.yaml` |
| Hook files | `hooks/<hook-name>.py` | `hooks/guard-no-direct-main.py` |
| Prompt files | `prompts/<role-name>.md` | `prompts/backend-developer.md` |
| Postgres LISTEN channels | `task_<uuid>_messages`, `agent.dispatched`, etc. | Free-form but documented per use |

---

## 11. External Concepts We Reference

These terms come from external sources but are used in Praxant docs and code. Defined here so usage is consistent.

| Term | Source | Definition |
|---|---|---|
| **Domain-Driven Design (DDD)** | Eric Evans (2003 book) | The design philosophy that strategic software design should reflect the domain it serves. Source of "ubiquitous language" itself. |
| **Ubiquitous language** | DDD | Shared vocabulary across developers, domain experts, and (for Praxant) AI agents. This file IS Praxant's ubiquitous language. |
| **MCP (Model Context Protocol)** | Anthropic | Standard for exposing tools to AI models. Praxant uses MCP for the human-input tool (and possibly future tools). |
| **OAuth / OAuth Token** | RFC 6749 et al. | The authentication protocol Praxant uses for subscription-based AI providers. `CLAUDE_CODE_OAUTH_TOKEN` is one example. |
| **Apache 2.0 License** | Apache Software Foundation | Praxant's chosen license. Permissive, with patent grant + trademark protection. |
| **DCO (Developer Certificate of Origin)** | Linux Foundation | Praxant's contributor agreement model. Each commit must have `Signed-off-by:` trailer. Lighter than CLA, preserves relicensing optionality. |
| **TDD (Test-Driven Development)** | Kent Beck et al. | The practice of writing tests before implementation. Encouraged for Praxant code; required by phase-boundary skills (`python-testing-phase`). |
| **Skill (in the sense Praxant uses)** | Praxant | A markdown file with frontmatter encoding procedural knowledge. NOT the same as "Anthropic Skills" or any specific vendor's skill format. |
| **Hook (in the sense Praxant uses)** | Praxant | A gating/validation mechanism that runs at specific lifecycle points. NOT the same as "Git hooks" or "React hooks." |

---

## 12. How to Add a Term to This File

1. Identify a term you've used in conversation, code, or docs that isn't here.
2. Write a one-line precise definition.
3. Add it to the appropriate section.
4. Add a usage note that distinguishes it from related terms.
5. If it conflicts with an existing term, resolve the conflict (rename one, deprecate the other, or merge).
6. Open a PR with the change so other contributors can review and align.

A term should appear in this file BEFORE it appears in a major doc, code identifier, or marketing copy. If a contributor introduces a new term in code/docs without adding it here first, code review should flag this.

---

## 13. How to Retire a Term

If a term is deprecated (replaced by a better term, or the underlying concept removed):

1. Move the term to the **Anti-Vocabulary** section (§9) with a "Use instead: <new term>" note.
2. Search the codebase and docs for usages and update them.
3. Don't delete the row — leave it as a permanent record so contributors searching for the old term find the new one.

---

*This file is the source of truth for what every Praxant term means. When in doubt, this file wins. When this file is wrong, fix it here first, then fix everywhere else.*
