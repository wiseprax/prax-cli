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

## What Has Already Been Proven

One important thing is already clear:

the agent workflow model works.

The work done in `claude-ai` already proved several important ideas:

- agent roles are useful
- visible workflow is better than invisible prompting
- state transitions matter
- structured handoffs matter
- deterministic rules should live in code
- repo-native coordination works

So the question is no longer whether those ideas are viable.

They are viable.

The question now is what form they should take if the goal is a broader open-source platform.

---

## Why The Current Form Is Not The Final Form

A working internal system is not automatically the right product foundation.

The current form is limited because it is:

- centered on Claude rather than runtime abstraction
- shaped around one organization's workflow language
- tied to CI/CD-era execution assumptions
- rigid about human approval
- optimized for one operator who already understands the system

Those are not failures.

They are normal characteristics of a system that was built to work first.

But if the goal is a reusable platform, those assumptions need to be generalized.

---

## Why Local Execution Is The Right Direction

Both of us already agree on the main architectural shift:

CI/CD pipelines are not the right long-term execution model for this.

The updated `claude-ai` docs already show movement in that direction: direct CLI invocation is now part of the operating guidance, which means the convergence is no longer theoretical.

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

Two bodies of prior work shape its design:

- the `claude-ai` work provides workflow discipline, lifecycle logic, role design, and operational lessons
- the broader autonomous-agent OSS ecosystem (Multica, OpenHands, Aider, Cline, FrankClaw) provides architectural patterns for orchestration, container isolation, multi-CLI dispatch, and agent UX

The product opportunity is in the synthesis — and in the parts none of those references get right.

Praxant is a **clean-room implementation in Go**. It studies prior systems for what works and writes its own code. **No source code is forked from any of those references.** Multica was briefly considered as a fork base, but its "modified Apache 2.0" license — commercial-use carve-outs, anti-rebrand clauses, unilateral-relicensing rights — is incompatible with Praxant's Apache 2.0 commitment, sovereign-tech positioning, and grant-funding strategy.

`claude-ai` alone is not enough.

The OSS ecosystem alone is not enough.

Together, with the execution model made explicitly local-first, the abstractions generalized, and the implementation written cleanly under a permissive OSI-approved license, there is a real platform.

---

## What Praxant Changes

Praxant keeps the strongest parts of the current internal workflow model:

- role specialization
- visible workflow
- deterministic policy logic
- structured progression through stages
- engineering work expressed through normal repo artifacts

But it changes the assumptions around them:

- from Claude-centric to runtime-neutral
- from internal workflow to product platform
- from fixed human gates to configurable automation policy
- from hybrid internal execution patterns to a clearly local-first container execution model
- from one operator's operating system to a platform other teams can adopt

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

This is the practical reason to take Praxant seriously:

the hardest conceptual work has already been partially done, but it is still trapped inside a narrower system shape.

What exists today is proof.

What is missing is productization.

That means:

- cleaner abstractions
- local execution as the default
- runtime neutrality
- configurable trust policy
- reusable workflows
- public OSS positioning

This is the point where a working internal pattern can become a real platform.

---

## The Shortest Honest Version

`claude-ai` proved that structured multi-agent workflow is real.`

`Praxant is the next step: turn that workflow into a local, runtime-neutral, configurable platform for teams using commercial coding agents.`

---

## Final Point

Praxant is not a critique of what already works.

It is the argument that what already works deserves a better final form.
