# Open SWE (LangChain) — and the Stripe / Ramp / Coinbase convergence

> **License:** MIT — `github.com/langchain-ai/open-swe`.
> **Stack:** Python backend on **LangGraph + Deep Agents**, web dashboard UI, experimental Electron desktop app.
> **Released:** 2026-03-17.
> **Shipping status:** shipped and self-hostable. No mandatory hosted component for the backend or dashboard — but **task execution requires a cloud sandbox provider** (Modal, Daytona, Runloop, E2B, or LangSmith), each of which is SaaS.
> **Why this file covers four projects:** Open SWE is explicitly published as the open-source distillation of three independently-built internal systems — **Stripe's Minions** (announced Feb 2026), **Ramp's Inspect**, and **Coinbase's Cloudbot**. Those three are closed and undocumented at the code level; Open SWE is the readable artifact. Treat it as one reference with four data points.
> **Status here:** architectural reference only. No source code is copied — not snippets, not utilities, not "just a few lines."

## What it is

An open-source framework for building **your organization's internal coding agent**: a Slack bot, CLI, and web app that takes engineering tasks and returns pull requests.

The invocation surfaces are the ones engineers already live in — mention `@openswe` in a Slack thread, comment on a Linear issue, or tag the bot on a GitHub PR. The bot acknowledges immediately, works in the background, and posts the PR link back into the originating thread. A deterministic thread ID routes follow-up messages to the same running agent, so a human can add instructions mid-task without restarting.

Each task runs in **its own isolated cloud sandbox** — a remote Linux environment with full shell access — with many tasks running in parallel across separate sandboxes.

The agent gets ~15 curated tools (`execute`, `fetch_url`, `http_request`, `commit_and_open_pr`, `linear_comment`, `slack_thread_reply`, `slack_add_reaction`, `output_iframe`, thread management, plus Deep Agents' native file operations), reads repo conventions from **`AGENTS.md`**, and pulls task context from the Linear issue or Slack thread before starting. Complex work decomposes to child agents through a `task` tool. Middleware hooks inject deterministic logic around the agent loop, and organizations are told to extend that with CI checks, visual verification, or review gates.

## The convergence finding — the important part

Three fintechs built this independently and landed in the same place:

| | Stripe **Minions** | Ramp **Inspect** | Coinbase **Cloudbot** | **Open SWE** |
|---|---|---|---|---|
| Build approach | fork-based | composition | from scratch | composition |
| Tool count | ~500 curated | — | — | ~15 curated |
| Sandbox | isolated cloud | isolated cloud | isolated cloud | isolated cloud, pluggable provider |
| Primary surface | Slack | Slack | Slack | Slack + Linear + GitHub |
| Output | PR | PR | PR | PR |

**Read this as validation, not threat** — the discipline in `README.md` says convergent design is evidence a primitive is right. Four independent teams choosing *chat as the control surface*, *per-task isolation*, *curated rather than maximal toolsets*, *subagent delegation*, and *PR-as-deliverable* is strong evidence those five decisions in `project-specs.md` are correct.

The **curated-toolset** point deserves emphasis because it cuts against instinct: Stripe reached ~500 tools and Open SWE ships ~15, but both frame the number as a *selection* result, not a coverage result. Neither treats "expose everything" as the goal.

## Where it differs in shape

The decisive difference is one axis, and it is WisePrax's founding axis.

| Dimension | Open SWE | WisePrax |
|---|---|---|
| **Execution location** | **isolated cloud sandboxes — Modal / Daytona / Runloop / E2B / LangSmith** | **local developer hardware; never CI/CD-runner-based** |
| Agent posture | *is* the agent (own LangGraph loop) | *wraps* commercial CLIs (SP-4) |
| Cost shape | per-token API billing + sandbox billing | subscription-billed CLI (Claude Max via `CLAUDE_CODE_OAUTH_TOKEN`) |
| Task source of truth | Linear issues + GitHub PRs | the user's VCS (Forgejo v1) |
| Workflow definition | middleware hooks in Python code | states/transitions in Postgres, authored from the admin UI |
| Chat surface | Slack (hosted SaaS) | Matrix — Tuwunel + Element, self-hosted |
| Secrets | not published | OpenBao short-lived per-task tokens, Vaultwarden upstream |
| Merge policy | lints/tests then opens PR; approval gates are middleware you add | human merge authority by default, never auto-merge |

Self-hosting the backend does not make the system sovereign. **The code that runs is executed by a third-party sandbox SaaS**, which means the repository contents, the credentials injected into the sandbox, and the execution telemetry all leave hardware the user controls. That is the exact gap `why-wiseprax.md` argues against, and it is structural — not a configuration option, because Open SWE has no local execution provider.

## Patterns worth borrowing (without code)

### 1. Deterministic thread ID as the mid-task input channel

A stable thread identifier routes follow-up chat messages into the already-running agent. **Adopt for SP-3 and SP-6.** WisePrax's message bus is Postgres `LISTEN`/`NOTIFY` with an MCP human-input tool, but the *addressing* question — how does a human's later message find the right running praxagent? — is answered cleanly here: the chat thread is the address. Matrix room/thread IDs map onto this directly.

### 2. Multi-surface invocation with a single acknowledgement contract

Slack, issue tracker, and PR comments are three entry points into one task model, and every one of them acknowledges immediately and reports back to the originating thread. **Adopt for SP-2 / SP-3 / SP-8.** The Kanban surface should not be the only way in; a Forgejo issue comment should be able to open a task, and the task should report back where it was invoked from.

### 3. Curated toolset as a stated discipline

Both Stripe (~500) and Open SWE (~15) frame tool count as a curation decision. **Adopt as doctrine for skills.** WisePrax's substrate-not-behavior tenet already pushes tools into skills; the addition here is that a *deliberate, reviewed* tool inventory is itself a quality lever, and that inventory belongs under the same review discipline as code.

### 4. Pluggable sandbox provider as an interface

Open SWE treats the sandbox as an adapter with several implementations and a custom-provider escape hatch. **Adopt the seam, invert the default.** SP-5's container runtime should be an interface, with **local Docker as the v1 and default adapter**. That keeps a remote-execution adapter available later — for a homelab GPU box, per `traycer.md`'s multi-host note — without conceding the local-first default.

### 5. `AGENTS.md` as repo-authored context

Third project in this directory to converge on it (Traycer, Open SWE, and Roast's repo-local prompt files). **Adopt** — the convention is settled.

### 6. Middleware hooks around the agent loop for deterministic logic

Injecting non-LLM logic before/after the loop is how Open SWE keeps determinism. **Compare to `roast.md`'s progressive determinism** — the same instinct, two mechanisms. WisePrax's equivalent is the SP-1 transition, which is stronger: a transition is durable, inspectable, and authored outside the code. Note the convergence; do not import the mechanism.

## Patterns to reject

- **Cloud-sandbox execution.** The load-bearing rejection. Local execution on user-controlled hardware is the founding premise; a SaaS sandbox dependency negates it regardless of how the control plane is hosted.
- **Owning the agent loop.** Open SWE competes with Claude Code; WisePrax supervises it. Building a loop on LangGraph would forfeit the runtime-neutrality axis (§4.3 of the matrix) and re-open the model-billing question settled in §4.1.
- **Slack as the chat substrate.** Hosted, closed, and a data-egress path. Matrix + Tuwunel was chosen for exactly this reason.
- **Linear as the task source of truth.** Same objection, plus it contradicts §4.2 — VCS owns task state.
- **PR-on-completion with approval as optional middleware.** WisePrax's default is human merge authority; "add a review gate if you want one" is the wrong default for a supervision platform.
- **Per-token API billing as the assumed cost shape.** §4.1.

## Why WisePrax is still different

Open SWE is the strongest open-source entrant this directory has surveyed on *organizational* coding-agent orchestration, and it arrives with the credibility of three production systems behind it. It takes nothing from §4.2 (VCS-of-truth), §4.3 (council), §4.5 (secrets broker), or §4.7 (default-deny egress) — none of which it addresses — and it takes a clear position *against* WisePrax on §4.1 (it is the agent, not a wrapper) and on sovereignty (cloud sandbox execution, Slack, Linear).

After Traycer narrowed §4.1, the remaining WisePrax claim is a conjunction, and Open SWE tightens it rather than breaking it:

**local execution on user-controlled hardware + a fully open-source orchestrator + wrapping subscription-billed commercial CLIs + VCS-as-source-of-truth + supervised-by-default.**

Traycer has the local half and a proprietary orchestrator. Open SWE has the open orchestrator and cloud execution. **Neither has both, and no surveyed project has the conjunction.**

## Where Open SWE is ahead today

Recorded honestly, per `COMPARISON.md` §6:

- It ships, is MIT, is self-hostable, and has LangChain's distribution behind it.
- Three named production deployments' worth of architectural validation. WisePrax has zero.
- The Slack ↔ issue ↔ PR loop is working software; SP-3 and SP-8 are specs.
- Mid-task follow-up messaging into a running agent — SP-6's headline feature — already works there.
- Parallel multi-task execution across isolated sandboxes is solved; SP-5 is not designed.
- The `AGENTS.md`-plus-thread-context assembly is a complete answer to a question SP-4 has not yet asked.

The uncomfortable summary: **Open SWE built SP-3 + SP-5 + SP-6 first, and gave it away.** The rebuttal is not feature parity — it is that every one of those features runs on someone else's computer.

## Cross-reference

- `COMPARISON.md` §2 (matrix row), §3 (placement), §4.1 / §4.2 (differentiator now a conjunction), §6 (honest weaknesses).
- `roast.md` — the opposite bet: local, library-shaped, deterministic-first, no orchestration.
- `traycer.md` — the other half of the pincer: local execution, proprietary orchestrator.
- `closed-source-competitors.md` — Stripe Minions / Ramp Inspect / Coinbase Cloudbot are closed; only their architecture is public, via this project.

## Open items for the charter

Need `project-specs.md` §11 entries when adopted:

1. **Chat thread ID as the canonical address for mid-task human input** (SP-3 + SP-6), replacing any bespoke session-addressing scheme.
2. **Multi-surface task invocation** — Forgejo issue/PR comment as a first-class task entry point alongside the Kanban surface (SP-2 / SP-8).
3. **Container runtime as a pluggable adapter interface with local Docker as the v1 default** (SP-5), preserving a future remote-execution adapter without conceding local-first.
4. **Curated tool inventory as a reviewed artifact** — doctrine for skills, not a platform feature.
