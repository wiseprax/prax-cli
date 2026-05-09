# Reference Project Analysis

This document captures what Praxant should borrow, adapt, and reject from the reference project at `/home/wellington/workspace/praxant.ai/claude-ai`.

It exists to prevent vague influence.

The goal is not to loosely say "Praxant is inspired by claude-ai." The goal is to state exactly which patterns are useful, which ones must be generalized, and which ones do not belong in Praxant at all.

---

## 1. Purpose

`claude-ai` is an internal multi-agent operating system built around Claude Code sessions, Forgejo, and company-specific workflows.

Praxant is conceptually different.

Praxant is intended to become a configurable product platform for orchestrating autonomous coding agents that run locally while using external commercial AI runtimes and providers.

So the right question is not:

- "Should we copy claude-ai?"

The right question is:

- "Which ideas from claude-ai are structurally strong enough to become productized in Praxant?"

---

## 2. High-Level Conclusion

Praxant should borrow the workflow discipline of `claude-ai`, but not its product identity.

What `claude-ai` does well:

- treats agent work as a visible workflow, not as hidden chat output
- uses repo-native primitives as coordination fabric
- separates deterministic logic from judgment tasks
- specializes agents by role
- encodes lifecycle stages explicitly

What Praxant must do differently:

- support multiple commercial runtimes, not just Claude-centric flows
- treat human approval as configurable policy, not fixed doctrine
- expose a general product vocabulary instead of company-specific language
- run as a local orchestration platform, not as an internal Claude hub

Short version:

`Borrow the operating discipline. Replace the assumptions. Productize the abstractions.`

Another way to say it:

`The current internal system proves the workflow works. Praxant is the form that can scale beyond one person, one company, and one runtime.`

---

## 3. Reference Materials Reviewed

The comparison is based primarily on:

- `claude-ai/README.md`
- `claude-ai/CLAUDE.md`
- `claude-ai/docs/technical/AGENT-WORKFLOW-ARCHITECTURE.md`
- `claude-ai/docs/internal/agent-delivery-lifecycle.md`

---

## 3.5 Why This Is The Next Step Even If The Current System Already Works

This is the most important point for anyone reading these docs with a working internal system in mind.

The question is not whether `claude-ai` works.

It does work.

The question is whether its current shape is the best foundation for a reusable product platform.

The answer is no, for structural reasons:

- it is centered on Claude rather than on runtime abstraction
- it is shaped around one organization's operating language
- although it has started moving execution out of pure CI/CD dispatch, it still reflects a hybrid system rather than a product-grade local orchestration platform
- it treats human approval as doctrine rather than as configurable policy
- it is optimized for a proven internal workflow, not for a broad product surface

What Praxant offers is not a repudiation of that system.

It is the productization path for its strongest ideas:

- keep the lifecycle rigor
- keep the role specialization
- keep the visible workflow
- keep the deterministic policy logic
- continue the move toward local execution and make it the product default
- generalize the abstractions
- support multiple commercial runtimes
- make autonomy configurable

That is why this is the way to go.

The current system is proof of concept by operation.
Praxant is the attempt to turn that proof into a platform.

---

## 4. Borrow / Adapt / Reject

### 4.1 Borrow

These ideas are strong in their current form and should influence Praxant directly.

#### A. Visible workflow over invisible prompting

In `claude-ai`, work is expressed through issues, PRs, comments, and labels. That is correct.

Why it matters for Praxant:

- developers can see what the agent is doing
- workflow state is auditable
- handoffs are inspectable
- team collaboration stays anchored in normal engineering artifacts

Praxant implication:

- repo and workflow surfaces must remain first-class
- agent actions should produce visible, inspectable artifacts

#### B. Deterministic logic in code, judgment in agents

`claude-ai` is right to insist that state transitions, rules, and workflow reactions should live in code rather than being re-explained in prompts.

Why it matters for Praxant:

- quality gates must be deterministic
- automation policy must be deterministic
- escalation rules must be deterministic
- only judgment tasks should be delegated to agents

Praxant implication:

- policy engine in code
- prompts for reasoning, review, explanation, and implementation judgment

#### C. Role specialization

The idea of BA, PM, Architect, Developer, QA, and Technical Writer roles is useful.

Why it matters for Praxant:

- different phases need different behaviors
- different tool permissions should exist per role
- different quality standards apply by stage

Praxant implication:

- roles should be first-class
- roles should compose prompts, tools, skills, limits, and permissions

#### D. Lifecycle-driven orchestration

The `claude-ai` lifecycle model is one of its strongest design elements.

Why it matters for Praxant:

- agent work should move through explicit stages
- stage transitions should trigger the next action
- the process should be understandable by humans

Praxant implication:

- model workflows as states and transitions
- let policies govern whether transition requires consensus, gates, or human approval

#### E. Coordination through normal developer systems

`claude-ai` treats Forgejo as the coordination fabric instead of inventing a hidden proprietary control plane.

Why it matters for Praxant:

- developers already live in repo, PR, and issue systems
- trust is higher when work stays in standard engineering surfaces
- adoption is easier when the workflow fits existing habits

Praxant implication:

- chat, VCS, and review systems should remain native integration points
- do not hide the process inside a sealed product UI

### 4.2 Adapt

These ideas are valuable, but must be generalized or reframed before they belong in Praxant.

#### A. Human approval gates

In `claude-ai`, Marins acts as the hard approval gate at nearly every important step.

This is valid for an internal operating model, but too rigid for Praxant.

Praxant should adapt this into:

- configurable automation policy
- configurable review policy
- configurable merge policy
- risk-based escalation

Praxant principle:

`Human approval is one policy option, not the universal law of the platform.`

#### B. Agent role taxonomy

The roles in `claude-ai` are useful, but too tied to a particular organization and process.

Praxant should adapt this into a generic role system such as:

- implementation agent
- review agent
- architect agent
- documentation agent
- QA agent
- operations agent

The exact role catalog should be configurable, not baked in as a company workflow.

#### C. The hub model

`claude-ai` acts as a Claude-centered workspace hub. That is structurally useful, but Praxant should not inherit the Claude-centric framing.

Praxant should adapt the hub concept into:

- orchestration core
- runtime abstraction
- policy engine
- provider-neutral role execution

Praxant principle:

`Claude Code may be one runtime. It must not define the product's identity.`

#### D. Agent account and dispatch patterns

The use of distinct agent identities and dispatch chains is good, but the current implementation is tightly coupled to Forgejo-specific accounts and organization structure.

Praxant should adapt this into:

- runtime-agnostic agent identities
- provider-neutral review council membership
- configurable dispatch triggers

The idea survives. The implementation shape must become product-grade.

#### E. Documentation and handoff discipline

`claude-ai` benefits from formal BRD/SRS/FRD handoff structure.

Praxant should keep the idea of structured handoff, but avoid unnecessary ceremony for simple workflows.

Praxant principle:

- structure when complexity requires it
- lightweight defaults when it does not

### 4.3 Reject

These ideas should not define Praxant.

#### A. Claude-centric identity

`claude-ai` is explicitly built around Claude Code as the center of gravity.

Praxant must reject that framing.

Why:

- Praxant is a wrapper/orchestration layer for multiple commercial agent tools
- runtime neutrality is part of the value proposition
- vendor lock-in at the identity layer would weaken the product story

#### B. Company-specific vocabulary and persona

Terms like `Code Sorcerer` work internally, but do not belong in Praxant.

Why:

- they are organization-specific
- they do not scale as product language
- they obscure the actual abstraction

Praxant should prefer:

- operator
- reviewer
- approver
- automation policy
- quality gate
- consensus policy

#### C. Hard-coded human merge doctrine

In `claude-ai`, the human always merges.

Praxant should reject this as a universal rule.

Why:

- it limits the product appeal
- it does not fit your stated vision
- many teams will want policy-authorized merges for low-risk cases

Praxant should instead support:

- human-reviewed mode
- guarded mode
- policy-authorized merge mode

#### D. CI/CD as the primary execution identity

The historical `claude-ai` system used automation patterns strongly tied to CI/CD runners and internal workflow machinery. The updated docs now show that Marins has already started moving important execution paths toward direct CLI invocation, which is a meaningful convergence with Praxant.

Praxant should still reject CI/CD as the defining execution identity.

Why:

- your product direction is local execution on a developer-controlled machine
- local execution is part of the differentiation
- agent orchestration should not depend on CI semantics as its primary mental model

CI remains useful for validation and selective automation, not as the defining execution environment.

#### E. Internal-ops identity instead of product identity

`claude-ai` reads like an internal operational system for a specific organization.

Praxant must reject that posture if it wants real adoption as a platform.

Why:

- internal workflow assumptions do not generalize automatically
- product vocabulary needs to be stable across many teams
- the public story must be simpler and sharper

---

## 5. What Praxant Should Turn Into First-Class Concepts

Based on the comparison, Praxant should elevate these concepts explicitly:

- `Runtime`
- `Provider`
- `Role`
- `Praxagent`
- `Automation policy`
- `Review policy`
- `Merge policy`
- `Quality gate`
- `Consensus policy`
- `Stage transition`
- `Escalation rule`

These are the abstractions that convert inspiration into product architecture.

---

## 6. Product-Level Upgrade Over Claude-AI

The key improvement Praxant should make over `claude-ai` is this:

`turn an internal agent operating model into a configurable platform`

That means:

- not one human's preferred workflow
- not one vendor's runtime
- not one organization's repo conventions
- not one fixed merge doctrine

Instead:

- configurable autonomy
- configurable quality gates
- configurable multi-agent review
- configurable provider mix
- maintainable code and visible workflow as non-negotiable outcomes

---

## 7. Implications for Praxant Messaging

This comparison also sharpens the public positioning.

Praxant should not market itself as:

- a Claude system
- a hosted copilot
- an autonomous replacement for engineering judgment

Praxant should market itself as:

- a local orchestration layer for commercial coding agents
- a configurable workflow engine for AI-assisted software delivery
- a platform where agents can implement, review, challenge each other, and advance only when policy allows

Strong messaging themes:

- delegate the heavy lifting
- keep the engineering judgment
- maintainable code remains the standard
- teams decide what agents are allowed to automate

---

## 8. Final Decision Summary

### Borrow

- visible workflow
- deterministic state transitions
- role specialization
- lifecycle-driven orchestration
- repo-native coordination

### Adapt

- approval gates into configurable policy
- role catalog into product-grade abstractions
- hub model into provider-neutral orchestration core
- structured handoffs into lightweight-or-formal workflow options

### Reject

- Claude-centric identity
- company-specific language
- fixed human-only merge doctrine
- CI/CD-first execution identity
- internal-ops framing as product posture

---

## 9. One-Sentence Summary

Praxant should take the operational rigor of `claude-ai`, strip away the Claude-specific and organization-specific assumptions, and turn the remaining ideas into a configurable local orchestration platform for commercial coding agents.
