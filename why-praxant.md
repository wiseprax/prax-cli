# Why Praxant

This document is the short version.

If the longer charter and analysis docs are the architectural argument, this is the strategic one:

why this project should exist, why it should exist now, and why it should be built as a product platform rather than remain an internal workflow system.

---

## The Core Thesis

The current generation of coding agents is strong enough to do real work.

What is still weak is the workflow around them.

Today, teams usually end up in one of two unsatisfying positions:

- use a vendor-hosted product and give up control of execution and workflow
- use raw agent tools locally and build the process around ad hoc scripts, habits, and manual supervision

Praxant exists to solve that gap.

It is not trying to replace Claude Code, Codex, Kimi, Qwen, DeepSeek, or any other strong commercial tool.

It is trying to become the orchestration layer around them.

---

## The Design Principles That Hold

The structural shape of agent work that Praxant commits to is clear:

- agent roles are useful — different phases need different behaviors and permissions
- visible workflow beats invisible prompting — what an agent is doing should be inspectable
- state transitions matter — explicit lifecycle stages are better than hidden chat output
- structured handoffs matter — what one phase did and what the next phase needs has to be written down
- deterministic rules belong in code — gates, automation policy, escalation are not prompt content
- repo-native coordination wins — issues, PRs, comments, and labels are the right substrate

These are Praxant's design commitments. Each of them rules out a class of common implementation shapes.

---

## What Common Implementation Shapes Get Wrong

Most working agent setups today are limited because they are:

- centered on one model vendor rather than runtime-neutral
- shaped around one organization's workflow language
- tied to CI/CD-era execution assumptions
- rigid about human approval
- optimized for one operator who already understands the system

These limitations are not failures of effort. They are normal characteristics of systems built to work first, before being generalized.

A reusable platform needs different defaults — runtime neutrality, configurable autonomy, local-first execution, and a vocabulary that does not assume the reader is already inside one team's operating model.

---

## Why Local Execution Is The Right Direction

The main architectural shift is clear: CI/CD pipelines are not the right long-term execution model for autonomous coding agents. Agents are interactive, long-running, and need addressable supervision; CI runners are fire-and-forget. Forcing one into the other distorts both.

The better model is:

- run agents locally in containers on developer-controlled machines
- keep workflow connected to normal engineering tools
- use official commercial agent runtimes and provider accounts
- keep policy, visibility, and escalation under team control

That direction is better because it reduces the friction and distortion that come from forcing autonomous agents into CI/CD semantics.

CI should validate.

It should not be the primary home of agent execution.

---

## Why This Is More Than A Pile Of Borrowed Parts

Praxant should not be thought of as a pile of borrowed parts.

The broader autonomous-agent OSS ecosystem (Multica, OpenHands, Aider, Cline, FrankClaw, Paperclip, Goose) supplies architectural patterns for orchestration, container isolation, multi-CLI dispatch, and agent UX. None of those projects assembles those patterns the way Praxant does, and none of them combines them with: VCS-as-source-of-truth, configurable workflow engine, multi-model review council, live mid-task message-bus supervision, two-tier secrets brokered through OpenBao, and a strict substrate-not-behavior boundary.

The product opportunity is in the synthesis — and in the specific stances none of those references take.

Praxant is a **clean-room implementation in Go**. It studies prior systems for what works and writes its own code. **No source code is forked from any of those references.** Multica was briefly considered as a fork base, but its "modified Apache 2.0" license — commercial-use carve-outs, anti-rebrand clauses, unilateral-relicensing rights — is incompatible with Praxant's Apache 2.0 commitment, sovereign-tech positioning, and grant-funding strategy.

With the execution model made explicitly local-first, the abstractions generalized, and the implementation written cleanly under a permissive OSI-approved license, there is a real platform here that the rest of the ecosystem has not produced.

---

## What Praxant Stands For

Praxant commits to the proven structural elements of agent work:

- role specialization
- visible workflow
- deterministic policy logic
- structured progression through stages
- engineering work expressed through normal repo artifacts

And it makes the assumptions around them explicit, where the broader field tends to leave them implicit:

- runtime-neutral rather than vendor-centric
- product platform rather than internal workflow
- configurable automation policy rather than fixed human gates
- local-first container execution rather than CI/CD-bound execution
- a platform other teams can adopt rather than one operator's operating system

---

## What The Product Should Stand For

Praxant should stand for five things:

### 1. Local control

Agents run under developer or team control, not inside a vendor-owned orchestration product.

### 2. External intelligence

The platform works with the best commercial AI runtimes instead of pretending to be a model company.

### 3. Configurable autonomy

Teams decide what agents are allowed to do:

- draft
- implement
- review
- comment
- approve
- merge
- escalate

### 4. Maintainable output

The goal is not maximum autonomy for its own sake.

The goal is code that humans can still understand, review, and maintain.

### 5. Workflow visibility

Agent work should be inspectable, reviewable, and grounded in normal engineering surfaces.

Nothing important should happen in the dark.

---

## Why This Project Is Worth Joining

The practical reason to take Praxant seriously:

structured multi-agent workflow is a real capability — but the published implementations are either vendor-hosted (Devin, Copilot Workspace), single-runtime (OpenHands, Aider), general-purpose-not-coding (Goose), or non-OSI-licensed (Multica). None of them combines sovereign local execution, runtime neutrality, VCS-as-source-of-truth, live mid-task supervision, multi-model review, and a permissive OSI-approved license.

What is missing is exactly that combination. That means:

- cleaner abstractions
- local execution as the default
- runtime neutrality
- configurable trust policy
- reusable workflows
- public OSS positioning

This is the point where the structural shape of supervised autonomous coding work can become a real, sovereign, team-scale platform.

---

## The Shortest Honest Version

`Structured multi-agent workflow for coding is real and worth committing to.`

`Praxant is the local, runtime-neutral, configurable, sovereign-by-design platform for teams using commercial coding agents under live supervision.`

---

## Final Point

Praxant is not a critique of what other systems do.

It is the argument that the right shape — sovereign, local, runtime-neutral, supervised, VCS-anchored, council-reviewed — has not yet been built, and is worth building.
