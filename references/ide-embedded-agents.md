# IDE-embedded agents (Cline, Cursor, Continue)

> **Category:** in-editor agent UX — different shape from Praxant.
> **Why grouped:** these tools live inside the developer's IDE (VS Code primarily) and operate against the file/editor surface, not against a VCS + task queue. They share enough DNA to be evaluated together.

## Cline (formerly Claude Dev)

- **License:** Apache 2.0.
- **Stack:** TypeScript VS Code extension.
- **What it is:** an in-IDE agent that proposes edits, runs commands, and shows diffs for human approval at every step.
- **Pattern to borrow:** the **explicit approval gate per step** — every action surfaced to the user for accept/reject before execution. Praxant uses configurable policy gates (SP-7 / SP-1) and emoji-reaction approvals in chat (SP-3), which is the same idea generalized across team chat instead of personal IDE.

## Cursor (agent mode)

- **License:** closed source.
- **Stack:** custom IDE (VS Code fork).
- **What it is:** an IDE with agentic editing built in. Pattern source only — code, model usage, and editor coupling are non-portable.
- **Pattern to study:** end-to-end UX of step-confirmation, diff review, and "accept all" defaults. Useful for SP-8 drill-down panel design.

## Continue

- **License:** Apache 2.0.
- **Stack:** TypeScript IDE extension (VS Code + JetBrains).
- **What it is:** an open-source autopilot for IDE editing, configurable per model/provider.
- **Pattern to borrow:** **provider-neutral model configuration** in user-editable YAML. Aligns with Praxant's Model abstraction.

## Why Praxant is a different category

These tools operate at the IDE / file surface. The developer is at the keyboard, in their editor, watching every diff. Praxant operates at the VCS / task / team surface — agents work on tasks dispatched from issues, deliver PRs, debate each other in a multi-model council, and surface to the operator through a kanban + chat. The developer can leave for lunch. These are complementary product shapes, not competing ones — a team can use Cline at the keyboard *and* Praxant for the background queue.

## Borrow / Adapt / Reject

### Borrow
- Per-step approval UX (from Cline) — adapted to chat reactions in SP-3.
- Provider-neutral model config (from Continue) — aligns with the Model abstraction.

### Reject
- The "agent lives in the IDE" assumption. Praxant agents live in containers on the user's hardware, observable through a kanban, not in the editor.
