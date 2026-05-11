# Aider

> **License:** Apache 2.0 — clean-room-friendly to study.
> **Stack:** Python CLI, git-aware.
> **Distribution:** `pip install aider-chat`; runs locally as a terminal app.
> **What it is:** a pair-programming CLI that drives an LLM to edit code in a local git repo, committing each change. Best in class at *git-patch-shaped* agent work — every action produces a real commit.

## Patterns worth borrowing

- **Git-patch-oriented PR work.** Every agent action is a git commit on a working branch. State is captured in git, not in a hidden in-memory session. This aligns with Praxant's VCS-as-source-of-truth stance.
- **Conversation-driven workflow.** The CLI loop keeps a dialogue with the user; the agent does not silently run for minutes. A useful baseline for how live supervision *feels* in the small.
- **Repo-map / context summarization.** Aider's repo-mapping for large codebases is a well-studied solution to the "what does the LLM need to see?" problem. Skill authors may want similar primitives.

## Where Praxant intentionally diverges

- **Single-user vs. orchestration platform.** Aider is one developer + one LLM at a terminal. Praxant is one or many supervised agents working tasks dispatched from a VCS, visible to a team, reviewed by a council.
- **No containerization.** Aider runs on the developer's host. Praxant runs each praxagent inside a Docker container with default-deny egress.
- **No workflow engine.** Aider has no state machine, no actor types, no transitions — it's a single REPL. Praxant's SP-1 includes a configurable workflow engine.
- **No team-wide enforcement.** Aider is personal tooling. Praxant's substrate-not-behavior tenet specifically requires team-wide enforcement of workflow shape (see `project-specs.md` §4).

## Borrow / Adapt / Reject

### Borrow
- Git-as-state. Every agent step produces an inspectable artifact (commit, diff).
- Conversation-driven supervision shape — the human can interrupt the next step.

### Adapt
- Repo-map context-selection ideas — likely belong in skills, not the platform (substrate-not-behavior).

### Reject
- Single-user, single-runtime, terminal-bound shape. Praxant is multi-agent, multi-runtime, kanban-visible.

## Why Aider doesn't compete with Praxant

Different category. Aider is "an LLM-driven git pair-programmer." Praxant is "a local orchestration platform for autonomous coding agents under supervision." A Praxant skill could legitimately invoke Aider as one of several tools available to a praxagent — they compose, they don't compete.
