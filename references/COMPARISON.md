# Comparative Analysis: Praxant vs. Other Solutions

> **Purpose:** sharpen positioning. Make the "how is Praxant different from X?" answer reflexive.
> **Status:** v1 — designed to be extended. Add new rows / columns as the landscape moves.
> **Last updated:** 2026-05-11.

This document is the *single* comparative artifact. Per-project deep notes live in sibling files in this directory.

---

## 1. The axes that actually discriminate

Eight axes, chosen because they sort the field cleanly. Anything that fails to discriminate (e.g., "uses LLMs", "writes code") is not on this list.

| # | Axis | What it asks |
|---|---|---|
| 1 | **License posture** | OSI-clean + grant-eligible? Apache 2.0 / MIT / Mozilla → yes. AGPL → patterns-only (copyleft). **"Modified Apache 2.0" / BSL / SSPL / Elastic / Confluent Community** → source-available, *not* OSI-recognized, *not* grant-eligible (regardless of how the project markets itself). Closed source → no. |
| 2 | **Sovereignty** | Does it run on hardware the user controls, with secrets the user holds? Or hosted SaaS? |
| 3 | **Runtime neutrality** | Can it wrap arbitrary commercial agent CLIs (Claude Code, Codex, …), or does it bind to one? |
| 4 | **VCS as source of truth** | Does the user's VCS own task state, with the platform as a view? Or does the platform own task state in its own DB? |
| 5 | **Live supervision mid-task** | Can a human talk to a running agent without aborting and restarting? |
| 6 | **Multi-model review council** | Is multi-provider parallel review a first-class feature (round 1 blind, round 2+ sighted)? |
| 7 | **Secrets discipline** | Are agents handed short-lived per-task tokens, or do they see master credentials? |
| 8 | **Substrate-not-behavior boundary** | Does the platform own only supervision/control/persistence/transitions/adapters, with all per-step behavior in skills/agents? |

## 2. The matrix

Legend: ✓ = yes, ✗ = no, ~ = partial / not stated in public docs, — = not applicable.

| Project | License | Sovereign | Runtime-neutral | VCS-of-truth | Live supervision | Council | Short-lived secrets | Substrate boundary |
|---|---|---|---|---|---|---|---|---|
| **Praxant** | Apache 2.0 | ✓ local | ✓ (SP-4) | ✓ (Forgejo first) | ✓ (SP-6 msg bus + MCP) | ✓ (SP-7) | ✓ (OpenBao broker) | ✓ (explicit tenet) |
| Paperclip | MIT | ✓ local | ✓ | ✗ ticket-based | ~ not stated | ✗ | ✓ run JWTs | ~ not stated |
| Goose (AAIF / LF) | Apache 2.0 | ✓ local desktop | ✓ 15+ providers | ✗ no VCS task model | ✓ desktop user present | ✗ | ~ provider creds | ~ MCP exts, single loop |
| OpenHands | MIT | ✓ local (or cloud) | ✗ own loop | ~ partial | ~ step-level only | ✗ | ✗ API keys | ✗ bundled |
| Multica | BSL-style (marketed as "modified Apache 2.0") | ✓ local | ✓ multi-CLI | — | — | — | — | — |
| Aider | Apache 2.0 | ✓ local | ✗ single REPL | ✓ git-native | — single-user | ✗ | ✗ env vars | — N/A |
| Cline | Apache 2.0 | ✓ local IDE | ✗ Claude-leaning | ~ filesystem | ✓ per-step gate | ✗ | ✗ env vars | — IDE shape |
| Continue | Apache 2.0 | ✓ local IDE | ✓ provider config | ~ filesystem | ✓ per-step | ✗ | ✗ env vars | — IDE shape |
| Cursor (agent) | closed | ✗ hosted | ✗ Cursor models | ~ filesystem | ✓ per-step | ✗ | n/a | — IDE shape |
| Devin / Cognition | closed | ✗ hosted SaaS | ✗ own stack | ✗ owned by them | ~ chat-style | ✗ | n/a | — opaque |
| Copilot Workspace | closed | ✗ hosted | ✗ Copilot only | ✗ GitHub.com only | ~ limited | ✗ | n/a | — opaque |
| Sweep AI (archived) | Apache 2.0 | ✓ self-host | ✗ one model | ✓ GitHub-first | ✗ | ✗ | ✗ env vars | — |

The "—" cells mean the axis isn't applicable to that product's shape (IDE-embedded tools don't have a "live supervision message bus" because the developer *is* the supervision channel). They're not negatives — they mark a category boundary.

## 3. Where each project actually lives

One paragraph each. Not a substitute for the per-project files in this directory.

- **Praxant** — local orchestration platform for *supervised* autonomous coding agents, with VCS as source of truth, configurable workflow engine, multi-model review council, and a live supervision message bus. Apache 2.0, Go, single binary, runs on the user's developer hardware.
- **Paperclip** — "open-source orchestration for zero-human companies." Broader scope than coding; org charts, budgets, governance. Heartbeat-style execution, ticket-based task model, short-lived run JWTs. MIT, TypeScript/Node. **Direct adjacent.**
- **Goose (AAIF / Linux Foundation)** — general-purpose local AI agent. Native desktop app + CLI, 15+ provider integrations, 70+ MCP extensions. Rust + TypeScript, Apache 2.0. **Different shape (general-purpose personal agent, not team-scale coding orchestrator), but convergent on local-first / MCP / provider neutrality.** Moved from `block/goose` to `aaif-goose/goose` under the Agentic AI Foundation.
- **OpenHands** — OSS autonomous software-development agent (renamed from OpenDevin). Single-agent shape with its own opinionated CodeAct-style loop, Docker isolation, live UI. MIT, Python. **Different posture (owns the loop, doesn't wrap).**
- **Multica** — multi-CLI dispatch platform with kanban+WebSocket UI. Architecturally close to Praxant but **source-available (BSL-style), not Apache 2.0** — the project markets a "modified Apache 2.0" license that the OSI does not recognize; in substance it sits in the BSL / SSPL / Elastic License category, with a commercial-use carve-out, anti-rebrand clause, and unilateral relicensing right. Patterns only — never a fork base. See `multica.md`.
- **Aider** — git-aware pair-programming CLI. Different category: single-user, single-runtime, terminal-bound. A Praxant skill could invoke Aider.
- **Cline / Continue / Cursor (agent mode)** — IDE-embedded agents. Different surface (the editor) and different supervision model (the developer is always present). Complementary to Praxant, not competitors.
- **Devin / Copilot Workspace / Cursor Background** — hosted SaaS competitors. Closed source, opaque internals, vendor lock-in. They define the *category* but not the shape Praxant is competing in.
- **Sweep AI (archived)** — VCS-driven dispatch prior art; the architecture survived but the business didn't. Apache 2.0, readable.
- **LiteLLM / Terraform / Backstage / Errbot** — not competitors; pattern sources for specific Praxant primitives (model abstraction, adapter ecosystem, extension points, chat abstraction). See `infrastructure-patterns.md`.

## 4. Why Praxant is special

The headline argument. Each item below is something *no single other project on the matrix combines*.

### 4.1 Wrapping commercial runtimes, not replacing them

The autonomous-coding-agent space is split between two postures:

- *"We are the agent."* Devin, OpenHands, Aider, Cursor. They ship their own opinionated loop and you use it.
- *"We are the platform."* Paperclip, Goose, Multica, Praxant. The agent is pluggable.

Within the platform camp, Praxant is the one that explicitly wraps the **commercial-CLI subscription-billed** runtimes — Claude Code first, via `CLAUDE_CODE_OAUTH_TOKEN` against the Claude Max 20× tier. That's a deliberate cost-shape decision: the user already pays $200/mo for Claude; Praxant uses *that*, with no per-token markup. Paperclip wraps the same runtimes but doesn't make the subscription-billing path the default. OpenHands defaults to API-key billing. Goose supports both API-key and subscription/ACP access but is a per-developer desktop app, not a team orchestrator — different surface entirely.

### 4.2 VCS-as-source-of-truth (not the platform DB)

Praxant never owns task state authoritatively. Tasks, PRs, reviews, labels — all live in the user's VCS (Forgejo for v1). The platform DB stores transcripts and runtime state, but if you turned the platform off tomorrow, the work itself is still in your VCS.

Sweep AI (archived) shared this stance, but the project is no longer a viable platform — only a code reference. Paperclip's task model is ticket-based in its own DB. OpenHands' is in-app. The combination of *platform-grade* + *VCS-of-truth* + *sovereign* + *currently maintained* is empty in published open source.

### 4.3 Multi-model review council with anti-correlation discipline

SP-7's council pattern — round 1 *blind* (each reviewer sees only the change, not the other reviewers' opinions), round 2+ *sighted* (debate), hard-cap 3 rounds — is a Praxant invention as far as the surveyed projects go. The point is to reduce shared-blind-spot risk by getting independent perspectives from different model providers before any consensus pressure forms.

No other project on the matrix ships parallel-blind multi-model review.

### 4.4 Live mid-task human supervision

SP-6's per-task Postgres LISTEN/NOTIFY message bus, paired with an MCP tool the agent can call to ask the human a question mid-task without aborting — neither Paperclip nor OpenHands publishes an equivalent. IDE-embedded agents (Cline, Cursor) get this for free because the developer is at the keyboard; Praxant achieves the same outcome for *background, queued* agent work, which is a harder problem.

### 4.5 Two-tier secrets (broker pattern)

Agents never see master credentials. OpenBao issues short-lived per-task tokens to the praxagent; Vaultwarden remains the human-managed source of truth and feeds OpenBao upstream. Paperclip uses short-lived run JWTs (closest analogue); OpenHands and the IDE-embedded tools mostly pass env vars through.

Independent convergence between Praxant and Paperclip on short-lived per-run tokens is reassuring — it suggests both teams arrived at the same primitive without copying each other.

### 4.6 Substrate-not-behavior tenet

This is the architectural discipline most other projects don't have. Per `project-specs.md` §4: the platform owns **supervision, control, persistence, deterministic transitions, and adapter abstractions** — nothing else. Code-quality rules, policy-as-code, per-step prompts, domain review criteria — all live in skills, not the platform. A four-part test gates every candidate feature.

Aider, Cline, OpenHands bundle behavior into the platform (the prompts, the loop, the review criteria are all baked in). Paperclip is closer (skills load behavior) but doesn't publish the explicit boundary test.

The tenet is *why* Praxant stays small and *why* skill authors can extend it without forking.

### 4.7 Default-deny container egress allowlist

Every praxagent runs in a Docker container with a default-deny egress allowlist (Anthropic API, Forgejo, package mirrors). `threat-model.md` calls this *"the single highest-leverage security control."* Paperclip's containerization story is not stated. OpenHands runs in containers but doesn't default to egress lockdown. The hosted SaaS competitors have this internally but you can't inspect or change it.

### 4.8 Apache 2.0 + DCO + clean-room Go + grant-eligible

The licensing posture is not glamorous, but it's load-bearing:

- **Apache 2.0** — OSI-approved permissive license, no relicensing right, no commercial-use carve-out. Multica's "modified Apache" is *not* this and was rejected for that reason.
- **DCO** (not CLA) — contributors keep their copyright; signing a CLA is not required.
- **Clean-room Go** — no source forked from any prior project. Every reference in this directory is studied for patterns only.
- **Grant-eligible** — NLnet, Sovereign Tech Fund, and similar funders effectively require OSI-approved licenses without carve-outs.

This is why Praxant can credibly position as sovereign-infrastructure-grade open source, where Devin/Cognition/Copilot Workspace structurally cannot, and Multica strictly cannot.

## 5. What Praxant is explicitly NOT trying to be

Honest anti-positioning helps. These framings exist elsewhere and Praxant intentionally rejects each:

- **Not an autonomous engineer ("just give it a ticket and walk away").** Praxant is *supervised*; that word is doctrine, not marketing.
- **Not an IDE-embedded copilot.** Cline, Cursor, Continue occupy that surface. Praxant operates at the VCS / task / team level.
- **Not a hosted SaaS.** Sovereignty is a positioning anchor, not an afterthought.
- **Not a "zero-human company" platform.** Paperclip's framing; Praxant stays inside the coding wedge.
- **Not a vendor-lock-in runtime.** Praxant wraps the runtime *you* chose.
- **Not a chat-bot or a workflow builder or a prompt manager.** (These are Paperclip's own disavowals; Praxant agrees.)

## 6. Where competitors lead today (honest weaknesses)

Treating this as a marketing document only would be dishonest. Where Praxant is behind:

- **Devin / Cursor agents** ship today. Praxant is pre-implementation (May 2026). They have product-market fit signals; we have specs.
- **OpenHands** has a stable, mature OSS codebase, a live community, and recognition. Praxant has neither yet.
- **Paperclip's heartbeat queue and short-lived JWTs are working in production code.** Praxant has them as design decisions in `project-specs.md`, not as running software.
- **Goose has ~45k stars, Linux Foundation governance, and a working Rust binary.** Mind share and brand are real. Praxant's narrower wedge (coding, supervised, VCS-anchored, team-scale) is the differentiation — but Goose owns "trusted local AI agent" as a category label today.
- **GitHub Copilot Workspace** benefits from native GitHub integration that Praxant cannot match — and most teams are on GitHub.com today, not Forgejo. SP-2 will eventually need a GitHub adapter to reach that audience.

What we have today is conviction about the *shape* of the solution and a charter that documents the trade-offs. That is real, but it is not running software. The "why we're special" argument is provisional until SP-1 is built.

## 7. How to extend this document

Add new projects as they appear:

1. Create `references/<slug>.md` with the same shape as the existing files (license + stack + what-to-borrow/adapt/reject + Praxant-distinguishing remarks).
2. Add a row to the matrix in §2.
3. Add a paragraph to §3.
4. Re-examine §4 — if a new project occupies one of Praxant's distinctive axes, the differentiation argument needs updating, not the matrix.

Also update the index in `references/README.md` and (if the new project changes a decision) `project-specs.md` §11.
