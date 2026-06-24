# OpenHands (ex-OpenDevin)

> **License:** MIT — clean-room-friendly to study.
> **Stack:** Python backend (~77%), React/TypeScript frontend (~16%), Docker-based agent execution.
> **Distribution:** self-hosted via Docker; also offers a cloud variant.
> **Canonical repo:** `github.com/OpenHands/OpenHands`. The project was renamed from "OpenDevin" to "OpenHands" and the org moved accordingly. Various third-party forks of the older OpenDevin name still exist (e.g., `invariantlabs-ai/OpenDevin` — a low-star fork with no stated differentiation); they are not authoritative.
> **What it is:** an open-source autonomous software-development agent — the OSS answer to Devin. Runs a single agent (or a small agent ensemble) inside a Docker container, with a web UI for live observation and intervention.

## Patterns worth borrowing

- **Autonomous worker model with Docker isolation** — directly relevant to SP-5 (container runtime + Ralph Loop). OpenHands treats the container as the unit of agent execution; WisePrax does the same.
- **UI for live agent inspection** — terminal stream, file browser, action history. SP-8's per-task drill-down panel covers similar ground; OpenHands' implementation is worth studying as a UX reference.
- **Tool-use action loop with explicit step boundaries** — each iteration produces an action (bash command, file edit, browser interaction) that the user can inspect and, optionally, intervene on. Aligns with SP-6 (live supervision message bus).
- **Microagent / "system message" pattern** — composable per-task instruction bundles. Possible reference for how WisePrax skills compose against a praxagent.

## Where WisePrax intentionally diverges

- **Runtime neutrality.** OpenHands ships its own opinionated agent loop (their CodeAct-style controller). WisePrax wraps *other* runtimes (Claude Code first, others later) rather than replacing them. The praxagent is "Claude Code under supervision," not "a new agent loop."
- **VCS as source of truth.** OpenHands' task model is mostly in-app. WisePrax's task model lives in the user's VCS (Forgejo first), and the platform is a view over it.
- **Multi-model review council.** OpenHands runs one agent at a time; WisePrax's SP-7 introduces a parallel-blind / sighted-debate council of distinct providers.
- **Subscription-billed (not API-key-billed).** OpenHands defaults to API-key billing per provider. WisePrax's v1 path uses `CLAUDE_CODE_OAUTH_TOKEN` against the Claude Max 20× subscription — fundamentally different cost shape.
- **Default-deny container egress.** Not the OpenHands default model; explicitly in WisePrax's `threat-model.md`.

## Borrow / Adapt / Reject

### Borrow
- Docker-as-the-execution-unit pattern.
- Live action-stream inspection UI shape.

### Adapt
- Tool-use loop framing — WisePrax adapts it as the Ralph Loop with iteration / token / wall-clock caps inside the wrapped runtime, rather than as a WisePrax-owned controller.

### Reject
- The "WisePrax owns the agent loop" posture. WisePrax wraps; it does not replace.
- API-key-first billing assumption.
