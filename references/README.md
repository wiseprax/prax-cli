# References

> **Purpose.** Catalog of every project WisePrax studies as prior art, with the patterns we borrow, adapt, or reject explicitly named per project. Prevents vague "inspired by" framing and enforces the clean-room-Go discipline (`CLAUDE.md` → "Borrow patterns, copy no code — absolute").
> **Status.** v1, designed for iteration. Add new files as the landscape moves. The single comparative artifact is `COMPARISON.md` — extend it whenever you add a project.

## How to read this directory

- **`COMPARISON.md`** — the single comparative document. Matrix of all projects across 8 discriminating axes, plus the "why WisePrax is special" argument. Start here if you want the headline.
- **Per-project files** — one document per project (or per category for tightly related projects). Each has the same shape: license + stack + patterns to borrow + patterns to adapt + patterns to reject + why-WisePrax-is-different.

## Index

### Direct adjacent projects (studied for patterns)

| File | Project | License | Why studied |
|---|---|---|---|
| [paperclip.md](paperclip.md) | Paperclip | MIT | "Open-source orchestration for zero-human companies." Closest published shape in spirit — broader scope, ticket-based. |
| [goose.md](goose.md) | Goose (AAIF / Linux Foundation) | Apache 2.0 | General-purpose local AI agent. Native desktop + CLI, 15+ providers, 70+ MCP extensions. Different shape (personal, not team) but convergent on local-first / MCP / provider neutrality. |
| [openhands.md](openhands.md) | OpenHands (ex-OpenDevin) | MIT | OSS autonomous engineer with Docker workers; owns its agent loop rather than wrapping. |
| [multica.md](multica.md) | Multica | BSL-style source-available (marketed as "modified Apache 2.0") | Multi-CLI dispatch + kanban UI. **License is not Apache 2.0** despite the marketing label — has commercial-use carve-out, anti-rebrand clause, and unilateral relicensing right. Patterns only, never a fork base. |
| [aider.md](aider.md) | Aider | Apache 2.0 | Git-patch-shaped pair-programming CLI; different category but a possible composition partner. |
| [ide-embedded-agents.md](ide-embedded-agents.md) | Cline, Cursor, Continue | mixed | In-IDE agent UX; complementary category, not direct competitor. |

### Closed-source competitors (positioning only)

| File | Projects |
|---|---|
| [closed-source-competitors.md](closed-source-competitors.md) | Devin/Cognition, GitHub Copilot Workspace, Cursor Background Agents |

### Infrastructure pattern sources (not competitors)

| File | Projects | Pattern |
|---|---|---|
| [infrastructure-patterns.md](infrastructure-patterns.md) | LiteLLM, Terraform, Backstage, Errbot, Sweep AI | Provider/adapter abstractions, plugin architecture, multi-chat backends, VCS-driven dispatch. |

### The comparative document

| File | Contents |
|---|---|
| [COMPARISON.md](COMPARISON.md) | 8-axis matrix across all projects + "why WisePrax is special" + honest weaknesses + extension instructions. |

## Discipline reminders

- **No source code is copied** — not snippets, not utilities, not "just a few lines." Patterns only.
- **Cite specifically.** When a WisePrax design decision borrows from a reference, name the project and the specific pattern. Avoid vague "inspired by."
- **License posture matters.** Apache 2.0 / MIT / Mozilla → safe to study. AGPL → patterns only, never copy. "Modified Apache" or custom carve-outs → assume incompatible until verified; document the verification in `project-specs.md` §11.
- **Convergent design is reassuring, not threatening.** When WisePrax and another project independently arrive at the same primitive (e.g., short-lived per-run tokens), that's evidence the primitive is right, not evidence of overlap.

## Cross-references

- `project-specs.md` §11 — Decision log. Any reference that changes a decision must produce a dated entry there.
- `project-specs.md` Appendix B — Open-source references table. This directory supersedes that table; the appendix is preserved for charter integrity but will be marked superseded in a future edit.
- `CLAUDE.md` → "Borrow patterns, copy no code" — the hard rule this directory enforces.
