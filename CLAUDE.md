# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository status

This repo is **pre-implementation**. It currently contains only design documents — no source tree, no build system, no tests. Work in this directory is editing markdown specs, not writing code. Do not invent build/lint/test commands; there are none yet. When implementation begins, the source code will land in this same `praxagent/` directory (per project-specs.md §SP-0).

## What this project is

**WisePrax** is a local orchestration platform for autonomous coding agents. It wraps commercial agent runtimes (Claude Code, Codex, Kimi, Qwen, DeepSeek) instead of replacing them, and runs everything on developer-controlled hardware. The v1 product is **Praxagent** — see `README.md` for the public pitch and `why-wiseprax.md` for the strategic argument.

## Canonical vocabulary (use these terms exactly)

`ubiquitous_language.md` is the authoritative glossary — consult it before introducing or renaming any concept. The four identity terms that get confused most often:

- **WisePrax AI** — the umbrella organization behind the project. Use when referring to the team, the entity, governance, brand, or external positioning ("the WisePrax AI team", "WisePrax AI is the umbrella for…").
- **WisePrax** — the platform/project as a whole.
- **Praxagent** — a single supervised agent instance (containerized worker + Ralph Loop + caps + message bus + council eligibility).
- **Agent runtime / Agent CLI** — the underlying tool being wrapped (e.g., `claude`, `codex`).

A raw `claude` session is not a praxagent. A praxagent is the runtime *plus the wrapper*. `wiseprax.ai` is the canonical domain — not an identity term.

## Document map

| File | Role |
|---|---|
| `README.md` | Public-facing pitch and positioning |
| `why-wiseprax.md` | Strategic argument: why this project should exist |
| `project-specs.md` | **Project charter.** Vision, sub-project decomposition (SP-0…SP-9, SP-A…SP-D), phased roadmap, decision log. The meta-design that governs all subsequent work. |
| `ubiquitous_language.md` | Canonical vocabulary (DDD-style). Source of truth for terms. |
| `threat-model.md` | Security posture: developer-workstation threat model, *not* enterprise-datacenter. Reject hardening proposals that don't map to a realistic threat in §3. |
| `references/` | Directory of studied prior art. One file per project (or category), plus `COMPARISON.md` — the single matrix + "why WisePrax is special" doc. Start with `references/README.md`. |
| `launch-posts.md` | Drafts for HN / r/selfhosted / etc. announcement posts. |

When editing these documents, keep the **decision log** in `project-specs.md` §11 in sync — any reversal of a decision belongs there with a date and rationale.

## Architectural decisions already locked in

These are decided. Do not re-open them in casual edits — if a change is needed, update `project-specs.md` §11 explicitly with rationale.

- **License:** Apache 2.0 + DCO sign-off (not CLA, not AGPL)
- **Build approach:** clean-room Go implementation. **No source code is forked from any prior project.** Multica was briefly selected as a fork base (2026-04-28 morning) and reversed the same day after discovery that Multica ships under a Dify-style "modified Apache 2.0" — commercial-use carve-out, anti-rebrand clause, unilateral-relicensing right — incompatible with WisePrax's Apache 2.0 commitment, sovereignty positioning, and grant-funding strategy (NLnet / Sovereign Tech Fund effectively require OSI-approved licenses). Multica, FrankClaw, OpenHands, ai-jail, Aider, and Cline are studied for *architectural patterns* only.
- **Stack:** Go (Chi + sqlc + gorilla/websocket) + PostgreSQL 17 + Next.js 16 App Router + Docker. (pgvector was previously locked in as "inherited from fork base"; with the fork dropped it is on-demand, not in v0.1.)
- **Distribution:** Single Go binary via GoReleaser; deployed via `docker compose up`
- **Execution model:** Local on developer hardware (GPU PC / mini-PC / homelab), **never CI/CD-runner-based**
- **VCS / Chat / Agent / Model / Secret abstractions** with one v1 adapter each: Forgejo / Matrix (Tuwunel homeserver + Element client) / Claude Code / Claude Max subscription via `CLAUDE_CODE_OAUTH_TOKEN` / OpenBao. Matrix chosen over Mattermost for native OIDC (Tuwunel's built-in OAuth2/OIDC server → Keycloak, no MAS sidecar) and to avoid Mattermost's open-core paywall.
- **Two-tier secrets architecture:** OpenBao is the runtime broker that issues short-lived per-task tokens to agents; Vaultwarden remains the human-managed source of truth and feeds OpenBao upstream. Agents and skills only ever see OpenBao-issued ephemeral creds.
- **Source of truth:** the user's VCS (Forgejo for v1) — WisePrax never owns task state authoritatively
- **Auto-merge:** never enabled by default; human merge authority unless explicit policy says otherwise
- **Adapters are added on demand, not speculatively** (depth-before-breadth, YAGNI)
- **Configurable workflow engine:** workflow definitions (states / transitions / actor types / conditions / task dependencies) live in Postgres and are authored from the admin UI — no config-file editing. Skills act as actors via REST or MCP (interface TBD). Runtime execution is deterministic against a fixed definition; the definition itself is mutable on the server.

## Working principles when editing specs

- **Substrate, not behavior.** WisePrax is the substrate; behavior lives in skills and agents. The platform owns supervision, control, persistence, deterministic transitions, and adapter abstractions — nothing more. Code-quality rules, policy-as-code, per-step prompts, and domain review criteria belong in skills, not in the platform. When evaluating a feature, apply the four-part test in `project-specs.md` §4 (project config / dynamic / team-wide enforcement / poor fit for repo text files). If any test fails, the thing belongs in skills, agents, or worktree artifacts — not the platform. When unclear, default to *out*.
- **Charter governs sub-projects.** Anything in a sub-project doc that contradicts `project-specs.md` must either update the charter or be revised.
- **Pareto MVP.** v0.1 ships the 20% that delivers 80% of value (1 VCS, 1 chat, 1 agent runtime, 1 model, basic Kanban). Resist adding features to the v0.1 scope.
- **Threat-model discipline.** Security proposals must map to a realistic threat in `threat-model.md` §3. "Enterprise compliance theater" is an explicit rejection category.
- **Borrow patterns, copy no code — absolute.** WisePrax is a clean-room Go implementation. Reference projects (Multica, FrankClaw, OpenHands, ai-jail, Aider, Cline) are studied for *architectural patterns* only. Never copy source code — not snippets, not utilities, not "just a few lines." When citing prior art, name it specifically and say which pattern is being adopted. Avoid vague "inspired by." This is hard law, not a guideline — violating it would compromise Apache 2.0 purity and grant eligibility.

## Sub-project status

Sub-projects are defined in `project-specs.md` §7 with a dependency graph. The skill chain for each is: brainstorming → spec doc → writing-plans → implementation.

| SP | Name | Status |
|---|---|---|
| SP-0 | Architecture decision | Decided |
| SP-1 | Orchestrator spine + configurable workflow engine | **Next implementation work** |
| SP-2 | VCS provider abstraction + Forgejo adapter | Pending |
| SP-3 | Chat provider abstraction + Matrix adapter (Tuwunel + Element) | Pending |
| SP-4 | Agent runtime abstraction + Claude Code worker | Pending |
| SP-5 | Container runtime + image strategy + Ralph Loop | Pending |
| SP-6 | Live supervision message bus + MCP human-input tool | Pending |
| SP-7 | Multi-model review council | Pending |
| SP-8 | Kanban control surface + workflow authoring | Pending |
| SP-9 | Secrets provider abstraction + OpenBao adapter (Vaultwarden upstream) | Pending |
| SP-A | Positioning | Partially decided |
| SP-B | Repo bootstrap checklist | Pending |
| SP-C | Launch readiness + announcement venues | Pending |
| SP-D | Sustainability | Pending |
