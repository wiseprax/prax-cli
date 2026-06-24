# Paperclip (paperclipai/paperclip)

> **License:** MIT — clean-room-friendly to study.
> **Stack:** TypeScript / Node.js 20+ / React / PostgreSQL / pnpm workspace.
> **Distribution:** `npx paperclipai onboard`, Docker, or git clone + pnpm. Self-hosted; no SaaS offering.
> **Tagline (verbatim):** *"Open-source orchestration for zero-human companies."*
> **Surveyed:** 2026-05-11 (README + landing page only — not a code read).

## What it is

A Node.js + React platform that orchestrates AI agents to "run a business" — org charts, budgets, governance, goal alignment, ticket system, heartbeat execution. Wraps agent runtimes (Claude Code, Codex, generic CLI agents, HTTP/webhook bots) via an adapter system. Stated framing: not a chatbot, not an agent framework, not a workflow builder, not a prompt manager, not a single-agent tool, not a code review tool.

## Headline subsystems (from the README)

- Identity & Access
- Org Chart & Agents
- Work & Task System
- Heartbeat Execution — DB-backed wakeup queue with coalescing, budget checks, workspace resolution, secret injection, skill loading, adapter invocation
- Workspaces & Runtime — isolated execution workspaces (git worktrees, operator branches)
- Governance & Approvals
- Budget & Cost Control
- Routines & Schedules
- Plugins
- Secrets & Storage — encrypted local storage, provider-backed object storage; short-lived run JWTs
- Activity & Events
- Company Portability

## Genuine overlap with WisePrax

| Dimension | Both ship |
|---|---|
| Self-hosted, local-execution orchestration of commercial agent runtimes | ✓ |
| "Bring your own agent" — Claude Code, Codex, CLI agents, HTTP bots | ✓ |
| Postgres-backed spine with queue/wakeup mechanism | their *heartbeat queue* ↔ our SP-1 dispatcher |
| Short-lived per-run tokens to agents | their *short-lived run JWTs* ↔ our OpenBao broker |
| Sensitive values kept out of prompts | ✓ |
| Task-manager-shaped surface | ✓ |
| OSI-approved permissive license | ✓ |

## Where they diverge (WisePrax's wedge holds)

These are documentation absences in Paperclip's published README, not refuted claims — the underlying code may have more than the README states.

- **VCS as first-class concept:** not stated. No Forgejo/GitHub/GitLab adapter pattern; no "VCS is the source of truth" stance.
- **Chat adapter (Mattermost / Slack / Discord):** not stated.
- **Multi-model review council:** not stated. Single-agent role assignment within hierarchies.
- **Live mid-task supervision (message bus + MCP human-input tool):** not stated.
- **Default-deny container egress allowlist:** not stated. No Docker/sandbox/network restriction model described.
- **Configurable workflow engine (states/transitions/actor types):** not stated. Ticket-based with blocker dependencies; no explicit state machine.

## Borrow / Adapt / Reject

### Borrow (architectural patterns)
- **Heartbeat / DB-backed wakeup queue with coalescing** — worth comparing against the SP-1 dispatcher design.
- **Short-lived run JWT injection** — independent validation of the OpenBao-broker direction in SP-9.
- **"Looks like a task manager" UX framing** — counterpoint when refining SP-8's kanban shape.

### Adapt
- **Plugin / adapter registry pattern** — useful for VCS, Chat, Agent, Model, Secret adapters in WisePrax, but WisePrax's adapters are server-side interfaces in Go, not Node plugins.

### Reject
- **"Zero-human companies" framing** — WisePrax explicitly keeps a human in the loop (configurable policy, never assumed). The framing also implies broader scope than coding.
- **Org-chart-as-primary-abstraction** — WisePrax's primary abstraction is the *task* in the user's VCS, not a hierarchical org chart.

## Honest caveats

- The "64.4k stars" figure WebFetch returned is suspicious for a project on `v2026.428.0` — treated as unverified.
- This analysis is a one-pass README read, not a code review. Several "not stated" items may exist in the codebase.

## Why WisePrax isn't redundant against Paperclip

Different product shape. Paperclip = *"run a business with agents."* WisePrax = *"ship code with supervised coding agents; your VCS owns the truth."* Paperclip's surface area is broad (governance, budgets, org charts); WisePrax's is narrow (coding agents under live supervision with a multi-model review council and a configurable workflow engine). Convergence on heartbeat-style dispatch and short-lived JWTs is reassuring, not threatening — it means independent designs are landing on the same primitives.
