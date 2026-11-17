# References

> **Purpose.** Catalog of every project WisePrax studies as prior art, with the patterns we borrow, adapt, or reject explicitly named per project. Prevents vague "inspired by" framing and enforces the clean-room-Go discipline (`CLAUDE.md` → "Borrow patterns, copy no code — absolute").
> **Status.** v1, designed for iteration. Add new files as the landscape moves. The single comparative artifact is `COMPARISON.md` — extend it whenever you add a project.

## How to read this directory

- **`COMPARISON.md`** — the single comparative document. Matrix of all projects across 8 discriminating axes, plus the "why WisePrax is special" argument. Start here if you want the headline.
- **Per-project files** — one document per project (or per category for tightly related projects). Each has the same shape: license + stack + patterns to borrow + patterns to adapt + patterns to reject + why-WisePrax-is-different.
- **Strategy files** — a different shape, and deliberately so. These record *arguments* (market theses, business-model critiques), not implementations. They have no license or stack section because there is nothing to borrow; they exist to be argued with. Keep them clearly separated from prior-art files so "studied" never gets read as "borrowed from."

## Index

### Direct adjacent projects (studied for patterns)

| File | Project | License | Why studied |
|---|---|---|---|
| [traycer.md](traycer.md) | Traycer | MIT (clients + protocol only; **Host and cloud backends proprietary**) | Closest published shape to the WisePrax wedge and **shipping**: wraps subscription-billed commercial CLIs, agent-to-agent delegation, git-worktree isolation, team boards. Open-core, desktop-shaped, platform-owns-state. |
| [open-swe.md](open-swe.md) | Open SWE (LangChain) — plus Stripe **Minions**, Ramp **Inspect**, Coinbase **Cloudbot** | MIT | Open-source distillation of three independently-built internal coding agents. Slack/Linear/GitHub invocation, mid-task follow-up into a running agent, curated toolsets, subagent delegation, PR-as-deliverable. **Executes in third-party cloud sandboxes** (Modal/Daytona/Runloop/E2B) — the direct inverse of WisePrax's local-first premise. |
| [paperclip.md](paperclip.md) | Paperclip | MIT | "Open-source orchestration for zero-human companies." Closest published shape in spirit — broader scope, ticket-based. |
| [goose.md](goose.md) | Goose (AAIF / Linux Foundation) | Apache 2.0 | General-purpose local AI agent. Native desktop + CLI, 15+ providers, 70+ MCP extensions. Different shape (personal, not team) but convergent on local-first / MCP / provider neutrality. |
| [openhands.md](openhands.md) | OpenHands (ex-OpenDevin) | MIT | OSS autonomous engineer with Docker workers; owns its agent loop rather than wrapping. |
| [multica.md](multica.md) | Multica | BSL-style source-available (marketed as "modified Apache 2.0") | Multi-CLI dispatch + kanban UI. **License is not Apache 2.0** despite the marketing label — has commercial-use carve-out, anti-rebrand clause, and unilateral relicensing right. Patterns only, never a fork base. |
| [aider.md](aider.md) | Aider | Apache 2.0 | Git-patch-shaped pair-programming CLI; different category but a possible composition partner. |
| [ide-embedded-agents.md](ide-embedded-agents.md) | Cline, Cursor, Continue | mixed | In-IDE agent UX; complementary category, not direct competitor. |

### Workflow-engine prior art (SP-1)

| File | Project | License | Why studied |
|---|---|---|---|
| [roast.md](roast.md) | Roast (Shopify) | MIT | Not a competitor — a **workflow engine**, and the best published prior art for SP-1. Deterministic and LLM steps interleaved, step-level session replay, `agent` cog wrapping local coding CLIs. Its v0→v1 move *from* YAML *to* a Ruby DSL is the strongest counter-evidence to the charter's "definitions in Postgres, no config-file editing" decision — read `roast.md` before touching SP-1. |

### Closed-source competitors (positioning only)

| File | Projects |
|---|---|
| [closed-source-competitors.md](closed-source-competitors.md) | Devin/Cognition, GitHub Copilot Workspace, Cursor Background Agents |

### Infrastructure pattern sources (not competitors)

| File | Projects | Pattern |
|---|---|---|
| [infrastructure-patterns.md](infrastructure-patterns.md) | LiteLLM, Terraform, Backstage, Errbot, Sweep AI | Provider/adapter abstractions, plugin architecture, multi-chat backends, VCS-driven dispatch. |

### Strategic arguments (positioning only — not prior art)

| File | Source | Why studied |
|---|---|---|
| [services-vs-software-thesis.md](services-vs-software-thesis.md) | Sequoia's "sell work, not software" vs. the Côté/Flor commoditization rebuttal | Directly stress-tests the WisePrax business posture (platform as internal delivery engine, never sold). Records where the rebuttal is wrong (§3), where it is right (§4), and the three defensible positions that follow (§5). No code or architecture borrowed — argument only. |

### The comparative document

| File | Contents |
|---|---|
| [COMPARISON.md](COMPARISON.md) | 8-axis matrix across all projects + "why WisePrax is special" + honest weaknesses + extension instructions. |

## Discipline reminders

- **No source code is copied** — not snippets, not utilities, not "just a few lines." Patterns only.
- **Cite specifically.** When a WisePrax design decision borrows from a reference, name the project and the specific pattern. Avoid vague "inspired by."
- **License posture matters.** Apache 2.0 / MIT / Mozilla → safe to study. AGPL → patterns only, never copy. "Modified Apache" or custom carve-outs → assume incompatible until verified; document the verification in `project-specs.md` §11.
- **Check license *scope*, not just license *text*.** Multica taught that the license name can lie. Traycer teaches the second half: the license text can be clean while covering only part of the product. Traycer's MIT is vanilla — but it applies to the clients and wire protocol, and the Host and cloud backends are proprietary. Always ask *which components* the OSI license actually covers, and specifically whether the **orchestrator** is one of them. Record the answer in the per-project file's header.
- **Check *where the code runs*, not just where the control plane is hosted.** Multica taught that a license name can lie; Traycer taught that an honest license can cover only part of the product. Open SWE teaches the third variant: the orchestrator is MIT and fully self-hostable, yet every task executes inside a third-party sandbox SaaS. Self-hosting the control plane is not sovereignty. Always ask which machine runs the agent, and record the answer in the per-project file's header.
- **Convergent design is reassuring, not threatening.** When WisePrax and another project independently arrive at the same primitive (e.g., short-lived per-run tokens), that's evidence the primitive is right, not evidence of overlap.

## Cross-references

- `project-specs.md` §11 — Decision log. Any reference that changes a decision must produce a dated entry there.
- `project-specs.md` Appendix B — Open-source references table. This directory supersedes that table; the appendix is preserved for charter integrity but will be marked superseded in a future edit.
- `CLAUDE.md` → "Borrow patterns, copy no code" — the hard rule this directory enforces.
