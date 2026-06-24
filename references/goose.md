# Goose (aaif-goose / Agentic AI Foundation)

> **License:** Apache 2.0 — clean-room-friendly to study.
> **Stack:** Rust (~49%) + TypeScript (~46%), plus shell / Python / JavaScript supporting code.
> **Distribution:** native desktop app (macOS, Linux, Windows) + CLI binary + Docker + embeddable library / API.
> **Governance:** **Agentic AI Foundation (AAIF) at the Linux Foundation.** Moved from `block/goose` to `aaif-goose/goose` under AAIF.
> **Scale (as of survey):** ~45k stars, 132 releases, v1.33.1 on 2026-04-29, 4,407 commits on main. Actively maintained.
> **Surveyed:** 2026-05-11 (README + landing page only).

## What it is

A **general-purpose local AI agent** — not specifically a coding agent — that runs as a desktop app or CLI on the user's machine. Integrates 15+ LLM providers (Anthropic, OpenAI, Google, Ollama, …) and ships 70+ extensions through Model Context Protocol (MCP). Positioning is broad: "install, execute, edit, and test with any LLM," for code work, research, writing, automation, and data analysis.

## Patterns worth borrowing

- **Local-first execution by default.** Goose ships as a native desktop binary, not a hosted service. Validates WisePrax's local-execution stance from a category leader's market choice.
- **MCP as the extension primitive.** 70+ extensions are MCP-conformant. WisePrax's SP-6 already uses MCP for the human-input tool; Goose's adoption is independent evidence that the protocol scales for adapter-style extension.
- **Provider neutrality via standardized integrations.** ~15 providers behind a uniform interface — the same shape LiteLLM uses, applied at the agent layer. Reinforces WisePrax's Model abstraction (SP-4 boundary).
- **Foundation governance.** Moving under AAIF / Linux Foundation is a credible path for a project that needs to outlive any single sponsor — relevant context for WisePrax's SP-D (sustainability) thinking, though premature for v0.1.

## Where WisePrax intentionally diverges

- **General-purpose vs. coding-wedge.** Goose explicitly disclaims being code-specific ("not just a code-suggestion tool"); its surface includes writing, research, data analysis. WisePrax is narrower on purpose — the v0.1 wedge is supervised coding agents working from VCS tasks. Going general dilutes the supervision and review-council story.
- **Desktop app vs. team platform.** Goose is structured as a per-developer desktop tool. WisePrax is a team-scale orchestrator with a kanban control surface, label-driven dispatch, and chat-based approval — all of which presume more than one person watches the work.
- **No VCS-as-source-of-truth.** Goose's task model is not VCS-anchored. WisePrax treats Forgejo/GitHub/GitLab as authoritative.
- **No multi-model review council.** Goose can talk to multiple providers, but it doesn't ship a parallel-blind / sighted-debate consensus loop. WisePrax's SP-7 does.
- **No configurable workflow engine.** Goose is reactive (you talk to it, it works). WisePrax's SP-1 ships a state-machine engine with admin-authored transitions.
- **No live mid-task message bus across team members.** A desktop app gives the local user direct supervision; WisePrax's SP-6 message bus lets a remote operator intervene in a queued background task.

## Borrow / Adapt / Reject

### Borrow
- MCP as the extension surface (already aligned via SP-6).
- Local-first / native-binary distribution (already aligned via GoReleaser).
- Provider-neutral interface discipline.

### Adapt
- Foundation governance as a future option — interesting precedent for SP-D, not a v0.1 decision.

### Reject
- General-purpose framing. WisePrax's wedge is coding, supervised, VCS-anchored. Generality dilutes the differentiation.
- Desktop-app-as-primary-surface. WisePrax's primary surface is the kanban + chat; the desktop is the developer's normal workstation, not a WisePrax client.

## Why Goose doesn't compete with WisePrax

Different shape. Goose is a *general-purpose personal AI agent that happens to run locally.* WisePrax is a *team-scale orchestration platform for supervised coding agents anchored in your VCS.* A user could legitimately run Goose for personal writing/research and WisePrax for the team's coding queue — they don't share surface area.

But the convergent design choices are notable: Apache 2.0, local execution, MCP for extensions, provider neutrality, foundation-style governance as a sustainability path. Each is an independent vote for primitives WisePrax already chose.

## Honest caveats

- This is a one-pass README skim, not a code read or hands-on trial.
- The 45k stars and v1.33.1 release figures came from WebFetch and have not been independently verified.
