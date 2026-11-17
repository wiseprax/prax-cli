# Traycer

> **License (substance):** MIT — vanilla text, no carve-outs, verified against the `LICENSE` file at `traycerai/traycer`. Unlike Multica, the license marketing matches the license law.
> **License (scope caveat):** MIT covers **the clients and the wire protocol only.** `AGENTS.md` states plainly that "the Traycer Host and cloud backends remain proprietary." The orchestrator itself — the process that owns agents, worktrees, PTYs, and git state — is closed. This is open-core, not open-source-platform.
> **Stack:** TypeScript, Bun 1.3.12 workspaces + Nx, Electron desktop shell, Yjs-backed persistence records, local SQLite for durable Task data.
> **Shipping status:** shipped. Signed desktop builds for macOS / Linux / Windows via GitHub Releases, plus a CLI, plus a legacy IDE extension.
> **Status here:** architectural reference only. No source code is copied — not snippets, not utilities, not "just a few lines."

## What it is

Traycer markets itself as the "nerve center for agentic coding": a desktop workspace that runs multiple commercial coding agents in parallel inside a durable session called a **Task**, with shared context, agent-to-agent delegation, git-worktree isolation, and team collaboration on shared boards.

It wraps the same runtimes WisePrax targets — Claude Code, Codex, Cursor, OpenCode — on the same "bring your existing subscription" cost shape, plus its own metered inference tier as an upsell.

**This is the closest published project to WisePrax's §4.1 claim, and it ships today.** It is the most important entry in this directory for that reason.

## Where it differs in shape

Traycer is a **developer-present desktop application**. WisePrax is **background, queued, team-scale orchestration on server-shaped infrastructure**. That is not a small distinction — it decides the whole architecture:

| Dimension | Traycer | WisePrax |
|---|---|---|
| Surface | Electron desktop, tabs and panels | Kanban control surface + chat, over HTTP/WebSocket |
| Human posture | at the keyboard, driving | supervising queued work, interruptible mid-task |
| Source of truth | its own SQLite / Yjs records + cloud sync | the user's VCS (Forgejo v1) |
| Isolation | git worktree (filesystem separation) | Docker container + default-deny egress allowlist |
| Team layer | hosted cloud, per-seat billing, GitHub OAuth SSO | self-hosted, Keycloak/OIDC, no seats |
| Orchestrator | proprietary Host binary | Apache 2.0 Rust binary |

Worktrees are the point to be careful about: **a worktree is not a sandbox.** It separates files, not process, network, or credentials. Traycer has no published container or egress story. WisePrax's `threat-model.md` position that container egress lockdown is "the single highest-leverage security control" is untouched by Traycer.

## Patterns worth borrowing (without code)

### 1. Three-tier version discipline, negotiated per method

Traycer keeps three version schemes deliberately separate and forbids conflating them:

1. **npm semver** — distribution builds only.
2. **Per-method RPC schema versions** `{ major, minor }` in a versioned-RPC registry — negotiated at the client↔host handshake. The handshake **never** looks at package semver.
3. **Persistence schema versions** `{ major, minor }` — govern on-disk record compatibility, with same-major additivity rules and a review gate for frozen schemas.

**Adopt for SP-1 and SP-4.** WisePrax has two interfaces with exactly this problem: the agent-runtime adapter contract (SP-4) and the workflow-definition records that live mutable in Postgres (SP-1). A workflow definition authored under one platform version must keep executing deterministically after an upgrade — that is Traycer's persistence-schema problem verbatim. Per-method negotiation also lets one adapter lag the platform without pinning the whole release.

### 2. Capability decomposition instead of a single "can agents talk" boolean

Traycer splits inter-agent interaction into three capabilities with three different requirement sets:

| Capability | Requires |
|---|---|
| **Reference** another agent | nothing — always available inside a Task |
| **Read** its transcript | same user; Terminal-interface transcripts only readable on the owning Host |
| **Deliver** a message to it | same user; sender and receiver both local to the same Host; both runtimes A2A-capable |

Referencing is broadest, delivery narrowest. Undeliverable messages are **rejected, not queued** — explicit failure over silent buffering.

**Adopt for SP-6 and SP-7.** WisePrax's message bus should model these as three distinct permissions, not one. The council (SP-7) needs "reference + read" for reviewers while explicitly *denying* delivery during round 1 — that is precisely how blind review gets enforced mechanically rather than by prompt discipline. Traycer arrived at the decomposition for connectivity reasons; WisePrax gets a correctness guarantee out of the same primitive.

### 3. Published per-runtime capability matrix

Traycer documents, per coding agent and per interface, which of the three capabilities that runtime actually supports (Claude Code: all three on both interfaces; Codex and OpenCode: no Terminal-interface inbox; Cursor: Chat only). It does not pretend all runtimes are equivalent.

**Adopt for SP-4.** Each WisePrax agent adapter should expose a machine-readable **capability descriptor** — supports-mid-task-input, supports-A2A, supports-model-switch, supports-MCP — and the orchestrator should refuse to schedule work requiring an unsupported capability rather than failing at runtime. Runtime neutrality does not mean runtime uniformity; the abstraction must carry the differences, not hide them.

### 4. Run-location model for git worktrees

Per workspace folder, an agent run picks one of: **Local** / **New worktree** / **Existing worktree**. For a new worktree, the branch source may be the current branch, another local branch, a remote branch, or **Working tree** — which carries uncommitted changes into the new worktree. Per-folder **setup and teardown scripts** run on worktree creation, with explicit lifecycle states (`Creating` → `Setting up` → `Ready` / `Setup failed` / `Setup cancelled`) and a management surface for reaping stale worktrees.

**Adopt for SP-5.** This is a fully worked-out design for the question SP-5 has to answer anyway. The details worth taking specifically: the "Working tree" carry-uncommitted source, the per-folder setup/teardown hook, surfacing setup as observable states rather than a spinner, and treating orphaned-worktree cleanup as a first-class management task.

### 5. Immutability boundary between session types

Traycer distinguishes a **Chat**-interface agent (transcript is Task-backed, durable, portable, can be *cloned* onto another host as a new agent) from a **Terminal**-interface agent (bound to a PTY, cannot migrate, choices frozen at launch). Migration is honestly modelled as *clone-to-new-agent*, never as moving a live session.

**Adopt for SP-4 / SP-5.** WisePrax needs the same explicit line between durable task state and live process state, and the same honesty that a running container-bound praxagent cannot be teleported. Clone-with-context is the correct escape hatch.

### 6. Lineage as provenance, not authority

Child agents appear nested under their parent in the agents tree, but the hierarchy grants nothing: any agent can reference any other agent in the Task regardless of lineage. The tree answers "where did this come from," not "what may this do."

**Adopt for SP-7.** Keeps the council's permission model flat and policy-driven while preserving an auditable delegation trail.

### 7. Provider configuration shape

Per provider: enable/disable, credential, **CLI binary selection** (bundled / detected / custom path), provider-scoped environment variables, and default CLI arguments — all scoped per host, and kept **separate from shell-wide host environment variables**.

**Adopt for SP-4 and SP-9.** The provider-env / shell-env separation is the right seam for the OpenBao broker: short-lived per-task credentials belong in the provider-scoped set and never leak into the general shell environment.

### 8. `AGENTS.md` auto-detection

Traycer reads a repo's `AGENTS.md` for project-specific context automatically.

**Adopt.** Cheap, already a de-facto convention, and consistent with the substrate-not-behavior tenet: repo-authored behavior, platform-provided plumbing.

## Patterns to adapt, not copy

- **Host locality as an A2A constraint.** Traycer rejects a message when sender and receiver are on different Hosts, because delivery rides host-local process state. WisePrax's SP-6 bus is Postgres `LISTEN`/`NOTIFY` — broker-mediated, so cross-machine delivery is available *for free*. Take the capability decomposition (§2 above); do **not** inherit the locality restriction. This is a place where WisePrax's server-shaped architecture is straightforwardly better, and it should be stated as such.
- **Multi-host ownership.** Traycer's "each machine runs a Host that owns what it can reach; durable data syncs, live surfaces don't" is a sound model for laptop + GPU box. WisePrax should reach the same outcome through one orchestrator scheduling remote container runtimes, not through per-device peer orchestrators.

## Patterns to reject

- **Proprietary orchestrator with open clients.** The load-bearing rejection. Traycer's MIT repo is clients + protocol; the Host and cloud backends are closed. A user cannot audit, fork, or self-host the thing that actually runs their agents and holds their credentials. WisePrax's entire sovereignty argument requires the orchestrator itself to be Apache 2.0.
- **Cloud sync as the collaboration substrate.** Yjs/CRDT real-time co-editing over a hosted backend is genuinely good UX, and it is the wrong substrate here: WisePrax's collaboration primitive is the VCS. Tasks, reviews, and artifacts live in Forgejo; the platform is a view. Adopting CRDT co-editing would mean the platform owning state authoritatively — a direct violation of §4.2.
- **First-party metered inference.** Traycer sells its own inference credits alongside BYO-subscription. `README.md` → "Not a self-hosted foundation model," and being in the model business would compromise provider neutrality.
- **Per-seat SaaS billing and GitHub-OAuth-only SSO.** Seat management is a hosted-vendor concern. WisePrax's identity path is Keycloak/OIDC against self-hosted infrastructure.
- **YOLO Mode.** "Automates the entire workflow from planning through verification without manual intervention" is the anti-position — `README.md` §"What WisePrax Is Not" and the supervision doctrine reject unsupervised autonomy as a *default mode*. WisePrax expresses the same capability as policy-gated autonomy that a team dials up deliberately, per repository risk.
- **Electron desktop as the primary surface.** Different product shape; WisePrax's control surface is a served Next.js app (SP-8) reachable by a team, not a per-developer installed binary.
- **Worktree-only isolation.** Filesystem separation is not a security boundary. Container + default-deny egress stays non-negotiable.

## Why WisePrax is still different

Traycer is the first project on the matrix to occupy §4.1 (platform camp + wrapping subscription-billed commercial CLIs) as a *shipping* product. That single-axis differentiation is now gone and §4.1 has been narrowed accordingly.

What Traycer does not touch:

- **§4.2 VCS-as-source-of-truth** — Traycer owns Task state in SQLite/Yjs + its cloud.
- **§4.3 blind-then-sighted multi-model council** — Traycer has agent-to-agent review, but no anti-correlation discipline: no blind round 1, no debate rounds, no round cap.
- **§4.5 two-tier secrets broker** — provider credentials are stored and passed through; no short-lived per-task tokens published.
- **§4.6 substrate-not-behavior tenet** — Modes, Phases, YOLO, Verification, and Review are behavior baked into the platform, exactly the bundling the tenet exists to prevent.
- **§4.7 default-deny container egress** — no container isolation story at all; worktrees only.
- **§4.8 fully open, grant-eligible orchestrator** — MIT clients are grant-clean, but the Host is proprietary, so the sovereign-infrastructure claim is unavailable to them.

The honest summary: **Traycer proved the wedge is real and got there first with a better-polished single-developer surface. WisePrax's remaining claim is the sovereign, auditable, containerized, VCS-anchored, team-scale version of it.** That claim is narrower than it was before this project was surveyed, and it is still empty in published open source.

## Where Traycer is ahead today

Recorded honestly, per the discipline in `COMPARISON.md` §6:

- It ships. Signed desktop builds, a CLI, an IDE extension, a documentation site, an active changelog.
- Agent-to-agent delegation with child-agent lineage is working software, not a spec.
- Its worktree lifecycle design is more complete than SP-5 currently is.
- Cross-device durable-state sync is solved; WisePrax has not designed the equivalent.
- Its protocol-versioning discipline is more rigorous than anything currently written into `project-specs.md`.

## Cross-reference

- `COMPARISON.md` §2 (matrix row), §3 (placement), §4.1 (narrowed differentiator), §6 (honest weaknesses).
- Candidate decision-log entries for `project-specs.md` §11 — see the "Open items" note below.

## Open items for the charter

These are borrowings that change or add to decided design. They need `project-specs.md` §11 entries when adopted:

1. Three-tier version discipline (npm/dist, per-method RPC, persistence schema) applied to SP-1 workflow definitions and SP-4 adapters.
2. Machine-readable per-adapter capability descriptor in SP-4, with the scheduler refusing unsupported work.
3. Reference / transcript / delivery as three separate permissions in SP-6, with round-1 blind review enforced by *denying delivery* rather than by prompt.
4. Worktree run-location model in SP-5 — including the carry-uncommitted "Working tree" source and per-folder setup/teardown hooks.
5. Provider-scoped vs shell-scoped environment separation in SP-4, as the injection seam for OpenBao short-lived credentials (SP-9).
