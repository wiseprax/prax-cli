# Closed-source competitors

> **Category:** hosted, proprietary autonomous coding agents. **Cannot be studied for code or architectural detail** — only for product positioning, surface UX, and the gaps they leave open.
> **Why included:** every public conversation about autonomous coding agents now starts with these names. WisePrax's positioning must address them explicitly.

## Devin / Cognition

- **What it is:** a hosted autonomous software engineer. Operates in a cloud VM, browses, codes, tests, and submits PRs. SaaS-only.
- **WisePrax's anti-position:**
  - **Sovereignty:** WisePrax runs on your hardware, against your VCS, with your secrets. Devin runs on Cognition's hardware against credentials you hand them.
  - **Cost shape:** Devin is metered per-task on Cognition's billing. WisePrax uses your $200/mo Claude Max subscription with no per-task markup.
  - **Runtime neutrality:** Devin is one closed model stack. WisePrax wraps the runtime *you* chose (Claude Code first; others on demand).
  - **Auditability:** Devin's internals are opaque. WisePrax's full state lives in Postgres + your VCS — you can read every transcript, every transition.

## GitHub Copilot Workspace / Copilot agents

- **What it is:** GitHub's autonomous-issue-to-PR pipeline. Tightly bound to GitHub.com and Copilot Enterprise billing.
- **WisePrax's anti-position:**
  - **VCS sovereignty:** WisePrax integrates with Forgejo first (self-hosted, sovereign VCS), GitLab and GitHub later. Copilot Workspace requires GitHub.com — a non-starter for teams on sovereign infrastructure.
  - **Multi-runtime:** Copilot Workspace runs Copilot only. WisePrax runs whichever subscription/runtime you have.
  - **On-prem:** Copilot Workspace is hosted. WisePrax is local.

## Cursor Background Agents

- **What it is:** Cursor's hosted background-agent feature; runs in Cursor's cloud, integrates with the Cursor IDE.
- **WisePrax's anti-position:** same as Devin/Copilot — hosted, IDE-bound, proprietary. WisePrax is a VCS-centric local platform.

## What we learn from them

- **The market believes autonomous coding agents are real.** WisePrax doesn't need to argue the category exists; it argues only about the *shape* (sovereign, local, runtime-neutral, supervised).
- **The hosted SaaS shape is the default; the sovereign local shape is the open lane.** Devin, Copilot Workspace, and Cursor agents all chose hosted SaaS. The sovereign-local niche is largely unfilled at production-grade quality — that's WisePrax's lane.
- **Auditability and cost predictability are growing complaints.** Both map to WisePrax's positioning anchors in `project-specs.md` §2.

## Borrow / Adapt / Reject

### Borrow
- Nothing technical (closed source).

### Adapt
- The marketing rhetoric of "autonomous engineer," carefully — WisePrax's claim is *"supervised* autonomous coding agent," with the supervision and policy controls front and center. The unqualified "autonomous" claim has burned the category before.

### Reject
- The hosted-SaaS distribution model.
- Single-runtime lock-in.
- Opaque internals.
- The "no human in the loop" framing.
