# WisePrax

Local orchestration for autonomous coding agents.

WisePrax runs agents on your machine, under your control, while letting you use the commercial AI runtimes and providers that already work best for your team: Claude Code, Codex, Kimi, Qwen, DeepSeek, and others.

It is not another chat UI. It is not another closed SaaS copilot.

It is the control layer for teams that want agent execution, approvals, review flows, and source-code integration to stay on their own infrastructure.

WisePrax is a wrapper around those tools, not a clone of them and not an unofficial hosted proxy. The goal is to orchestrate official commercial agent runtimes in a developer-controlled workflow.

## Why WisePrax

Most agent products force a bad tradeoff:

- Keep everything in a vendor SaaS
- Or give up orchestration and glue local tools together by hand

WisePrax is built for the middle path:

- Agents run locally on developer-controlled machines
- Work stays connected to your own repos and chat systems
- You choose the agent runtime and AI provider
- Teams choose how much authority agents get

## Selling Points

- Local execution, real control. Run autonomous coding agents on your own workstation, GPU box, or homelab machine instead of pushing orchestration into a hosted black box.
- Bring your own AI stack. Use Claude Code, Codex, Kimi, Qwen, DeepSeek, and future runtimes through one orchestration layer.
- Wrap official tools instead of replacing them. WisePrax is designed to sit around commercial agent CLIs and provider integrations, so teams can keep using the tools they already trust.
- Provider-neutral by design. WisePrax is built around adapters, so your workflow does not need to be rewritten every time the model landscape shifts.
- Runtime and model configuration stay flexible. Providers, models, and execution behavior can be selected through WisePrax configuration and CLI-level orchestration instead of being hardwired into one vendor workflow.
- Works with developer-owned infrastructure. The system is designed to integrate with self-hosted VCS, chat, and secrets systems instead of assuming GitHub-only or SaaS-only workflows.
- Configurable autonomy. Teams can decide whether agents draft, review, open PRs, approve, merge, or escalate based on policy and quality gates.
- Better than one-off scripts. WisePrax treats agent work as an operational workflow: dispatch, supervise, review, and merge.
- Built for teams, not just solo prompting. The target is shared engineering workflow, not a single person running a terminal session in isolation.

## What WisePrax Is

WisePrax is a platform for orchestrating autonomous coding agents.

In WisePrax terminology:

- `WisePrax` is the platform
- `Praxagent` is a supervised agent instance
- An `agent runtime` is the underlying tool, such as Claude Code or Codex

A Praxagent is not just a raw CLI session. It is an agent runtime wrapped with:

- task dispatch
- isolation
- budgets and execution caps
- chat and repo integration
- review, consensus, and automation workflow

The wrapper approach matters:

- WisePrax can use supported commercial tools directly instead of pretending to be them
- Teams can keep official accounts, subscriptions, and authentication flows where applicable
- Provider and model choice can remain configurable over time
- The orchestration layer stays separate from the intelligence layer

## What WisePrax Is Not

- Not a self-hosted foundation model
- Not a replacement for Claude Code, Codex, Kimi, or DeepSeek
- Not a generic chatbot UI
- Not a promise of full autonomy without human review
- Not a “vibe coding” toy

WisePrax complements strong commercial AI systems. It gives them structure, control, and team workflow.

It is designed to work with commercial tools, not to circumvent them. Provider terms and policies still apply, but WisePrax's architecture is intentionally centered on wrapping official runtimes rather than proxying them through an opaque third-party service.

## Core Use Cases

- Run coding agents locally while keeping orchestration off vendor SaaS
- Connect agent work to your own issue, PR, and review flow
- Standardize quality gates, approvals, and escalation policy for autonomous code changes
- Keep the freedom to switch agent runtimes or model providers later
- Let developers configure which runtime, provider, or model should be used for a task
- Coordinate multiple agent roles and review passes in one system
- Let one agent implement while others review, challenge, and advance the workflow only when policy allows

## Product Direction

WisePrax is not trying to prove that agents can type code in a terminal.

That part already exists.

The product direction is to turn scattered agent capability into a maintainable engineering workflow:

- one agent can implement
- other agents can review
- different providers can challenge each other
- deterministic quality gates can evaluate the result
- the workflow can advance automatically or escalate to a human depending on policy

The long-term goal is not blind autonomy.

It is code that remains understandable by humans, with automation that teams can dial up or down based on trust, risk, and repository policy.

## Why Developers Should Care

The model layer is changing too fast to build your process around one vendor.

Today you may prefer Claude Code for implementation, Codex for edits, Qwen or DeepSeek for review, and Kimi for specific reasoning tasks. Six months from now that mix may change again.

WisePrax is the stable layer above that churn.

If your workflow is tied directly to one hosted product, you inherit its pricing, limits, product decisions, and lock-in. If your workflow is tied to a local orchestration layer, you keep leverage.

## Positioning

WisePrax is best described as:

**Local agent orchestration for teams using commercial AI runtimes**

Short versions:

- Autonomous coding agents under your control
- Run agents locally, use the best AI providers
- The orchestration layer for Claude Code, Codex, Kimi, Qwen, DeepSeek, and more

## Planned Architecture

The current project direction is:

- Local execution on a developer-controlled machine
- External commercial AI providers and agent runtimes
- Wrapper-style integration with official tools such as Claude Code and similar CLIs
- Configurable provider, runtime, and model selection through WisePrax parameters and orchestration
- Adapter-based integrations for VCS, chat, secrets, and model/runtime layers
- Configurable automation, review, and merge policy
- Multi-agent implementation and review workflow
- Consensus and quality-gate based progression where teams want it

This project is opinionated about control, but pragmatic about intelligence: keep execution local, use the strongest external AI systems available.

## Who It Is For

- Developers who want autonomous coding help without giving orchestration away to SaaS
- Teams using self-hosted or developer-controlled tooling
- Engineers who want provider flexibility instead of workflow lock-in
- Teams that want AI to do more of the heavy lifting without losing maintainability or visibility
- Early adopters who see agent runtimes as interchangeable execution engines, not permanent platforms

## Current Status

WisePrax is in early design and architecture definition.

The initial focus is narrow on purpose:

- excellent local orchestration
- strong runtime/provider abstraction
- clean developer workflow

Breadth comes later. The first job is to make the core solid.

## Name

`WisePrax` comes from `praxis` plus the agentive `-ant`:

- `praxis`: action, doing, practical work
- `-ant`: one who acts

The name fits the system because WisePrax is about agents that act inside real engineering workflows, not just assist in chat.

## Follow The Project

- Domain: `wiseprax.ai`
- Project: `praxagent`

If this direction matches what you want from developer tooling, the next step is simple: stop treating model vendors as your platform, and start treating them as pluggable intelligence behind your own control layer.
