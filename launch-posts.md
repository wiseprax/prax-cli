# Launch Posts

This file contains launch copy for WisePrax across the highest-signal developer channels.

Core positioning to keep consistent:

- WisePrax is local agent orchestration
- Agents run on your machine
- Commercial AI runtimes/providers remain external
- WisePrax wraps official tools instead of replacing or impersonating them
- The value is control, workflow, and vendor neutrality

## One-Line Description

WisePrax is a local orchestration layer for autonomous coding agents, built for teams that want agent execution under their control while using commercial AI runtimes like Claude Code, Codex, Kimi, Qwen, and DeepSeek through a wrapper-style workflow.

## Tagline Options

- Local orchestration for autonomous coding agents
- Run agents locally. Use the best AI providers.
- Autonomous coding agents under your control
- The control layer for Claude Code, Codex, Kimi, Qwen, and DeepSeek
- A wrapper layer for commercial coding agents

## Show HN

### Title options

- Show HN: WisePrax - Local orchestration for autonomous coding agents
- Show HN: WisePrax - Run coding agents locally with Claude Code, Codex, Kimi, Qwen, and DeepSeek
- Show HN: WisePrax - A local control layer for autonomous coding agents

### Post body

Hi HN,

I am building WisePrax, a local orchestration layer for autonomous coding agents.

The basic idea is simple: keep agent execution, approvals, and engineering workflow under your control on your own machine, while still using the best commercial AI runtimes and providers available today.

WisePrax is not a hosted copilot and not a new foundation model. It is a wrapper and control layer around tools like Claude Code, Codex, Kimi, Qwen, and DeepSeek.

The problem I am trying to solve is that current agent workflows usually force one of two bad options:

1. Put orchestration inside a vendor SaaS and accept lock-in.
2. Stitch local tools together manually with scripts and no real team workflow.

WisePrax aims for the middle path:

- local agent execution
- wrapper-style use of official commercial agent tools
- provider-neutral runtime abstraction
- integration with developer-controlled repo/chat workflows
- configurable autonomy and merge policy
- room for multi-agent review, consensus, and supervision

The project is still early, but the design direction is now clear. I would especially value feedback from:

- developers using Claude Code / Codex heavily
- teams trying to avoid workflow lock-in
- people running self-hosted or developer-controlled engineering stacks

Main question: if you wanted a serious local control layer for coding agents, what would be non-negotiable for you?

## HN Comment Replies

### If someone says "Why not just use Claude Code directly?"

Because the point is not replacing the runtime. The point is controlling workflow around the runtime: approvals, dispatch, review, supervision, and the ability to swap providers later without rebuilding the whole process.

### If someone says "Won't providers ban this?"

The intent is the opposite of circumvention. WisePrax wraps official tools and lets users authenticate and operate through supported commercial runtimes. That does not create a blanket guarantee about provider policies, but it is a much cleaner posture than pretending to be the provider or proxying hidden access through a third-party SaaS.

### If someone says "This is not really self-hosted if models are commercial"

Correct. The execution and orchestration are local; the intelligence layer is external. The value is local control plus provider neutrality, not pretending to self-host frontier models.

### If someone says "Why not just scripts?"

Scripts work for one-off automation. They break down when you want a repeatable team workflow with supervision, review, runtime abstraction, and consistent execution boundaries.

### If someone says "So humans always have to approve and merge?"

No. The better model is configurable trust. Teams should be able to decide whether agents may draft, open PRs, review, approve, merge, or escalate based on policy, tests, risk, and quality gates.

## X / Twitter

### Short post

WisePrax is a local orchestration layer for autonomous coding agents.

Run agents on your machine.
Keep approvals and workflow under your control.
Use the best external AI runtimes: Claude Code, Codex, Kimi, Qwen, DeepSeek, and more.

It wraps the tools. It does not try to replace them.

The runtime is not the platform. Your workflow is.

`wiseprax.ai`

### Thread

1. I am building WisePrax: a local orchestration layer for autonomous coding agents.

2. The premise is simple: agent execution should run under your control on your own machine, even if the intelligence layer comes from commercial AI runtimes like Claude Code, Codex, Kimi, Qwen, or DeepSeek.

3. Today the bad tradeoff is usually:
   use a hosted SaaS and accept lock-in
   or glue local tools together with scripts

4. WisePrax is aimed at the middle path:
   local execution
   wrapper-style integration with official tools
   provider-neutral orchestration
   configurable automation and review flow
   integration with your own tooling

5. I do not want the model vendor to also own the workflow layer.

6. The goal is to make the runtime replaceable while the engineering process stays stable. Providers and models should be configurable, not welded into the workflow.

7. Early project, but the direction is now clear.

8. If you care about local control for coding agents, I would love your feedback: what would you consider non-negotiable?

### Very short version

Run coding agents locally. Use the best AI providers. Configure the workflow.

That is the idea behind WisePrax.

## Reddit

Best-fit communities:

- `r/selfhosted`
- `r/programming`
- `r/opensource`
- `r/LocalLLaMA` only if framed carefully around orchestration and provider neutrality, not local weights

### r/selfhosted draft

I am working on WisePrax, a local orchestration layer for autonomous coding agents.

It is not a self-hosted model stack. The agents run locally on your own machine, but they can use commercial AI runtimes/providers like Claude Code, Codex, Kimi, Qwen, and DeepSeek.

The important detail is that WisePrax is meant to wrap those tools in a controlled workflow, not bypass them.

The reason I am building it is that I want the workflow layer to stay under developer control:

- local execution
- configurable autonomy
- integration with developer-controlled tooling
- less lock-in to one vendor workflow

The project is still early, but I would value feedback from people here because this seems like the right audience for the control/execution side of the problem.

What would you want from a serious local orchestration layer for coding agents?

### r/programming draft

I am building WisePrax, a local control layer for autonomous coding agents.

The aim is to separate orchestration from the model vendor:

- run the agent locally
- wrap official commercial agent tools instead of replacing them
- keep autonomy policy and workflow under your control
- use commercial AI runtimes like Claude Code, Codex, Kimi, Qwen, and DeepSeek behind a stable orchestration layer

I think the long-term problem is not just model quality. It is workflow lock-in.

Curious whether others see the same need.

## Lobsters

Lobsters responds better to concise and technical framing.

### Title

WisePrax: local orchestration for autonomous coding agents

### Body

I am working on a tool called WisePrax.

The goal is to keep agent execution and workflow local while allowing the underlying intelligence to come from commercial coding runtimes like Claude Code, Codex, Kimi, Qwen, or DeepSeek.

In other words: local control over orchestration, external providers for model capability, with WisePrax acting as the wrapper around official tools rather than a replacement for them.

The motivation is to avoid tying engineering workflow too tightly to one hosted vendor product.

Still early, but feedback on the architecture and positioning would be useful.

## LinkedIn

WisePrax is a new project focused on local orchestration for autonomous coding agents.

The idea is straightforward: teams should be able to run agent execution under their own control while still leveraging the strongest commercial AI runtimes and providers available.

Instead of treating one model vendor as the whole platform, WisePrax treats AI runtimes as replaceable components behind a stable workflow layer.

It is also designed as a wrapper around official commercial tools, which is important both ergonomically and from a provider-relationship standpoint.

That means:

- local execution
- configurable automation, approval, and review workflow
- developer-controlled integration points
- provider flexibility over time

The project is still early, but the positioning is now clear: local control, external intelligence, less workflow lock-in.

## GitHub / Forgejo Repo Description

Short description:

Local orchestration for autonomous coding agents using Claude Code, Codex, Kimi, Qwen, DeepSeek, and other commercial AI runtimes.

Alternative:

Run coding agents locally while using the best commercial AI providers through one developer-controlled orchestration layer.

Alternative:

Wrapper-based local orchestration for commercial coding agents and AI runtimes.

## Website Hero Copy

### Option 1

WisePrax

Local orchestration for autonomous coding agents

Run agents on your machine. Use the best AI providers. Wrap official tools. Configure the workflow.

### Option 2

WisePrax

Autonomous coding agents under your control

Local execution and provider-neutral orchestration for teams using Claude Code, Codex, Kimi, Qwen, DeepSeek, and more.

Let one agent implement, others review, and policy decide what happens next.

### Option 3

WisePrax

The control layer for coding agents

Keep execution local. Keep workflow stable. Keep your provider options open.

## Launch Notes

- Do not call WisePrax a fully self-hosted AI platform.
- Do not claim model sovereignty if you depend on commercial providers.
- Do not claim immunity from provider enforcement; say the architecture is aligned around wrapping official tools, not bypassing them.
- Emphasize local control, workflow ownership, and provider neutrality.
- Emphasize configurable autonomy rather than fixed human approval.
- Avoid hype like "AI engineers replacing teams."
- Speak to lock-in, control, and developer workflow instead.
