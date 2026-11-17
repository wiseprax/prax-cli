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

## Internal fintech agents: Stripe Minions, Ramp Inspect, Coinbase Cloudbot

- **What they are:** in-house, closed coding agents built for a single company's own engineers. Stripe's **Minions** (announced Feb 2026) are one-shot end-to-end coding agents against a curated toolset of ~500 tools; **Ramp Inspect** and **Coinbase Cloudbot** are the equivalents at those firms. None is a product; none is for sale.
- **Why they matter anyway:** all three converged independently on the same architecture — Slack as the control surface, an isolated cloud sandbox per task, deliberately curated (not maximal) toolsets, subagent delegation, PR-as-deliverable. LangChain's **Open SWE** was published as the open-source distillation of exactly that convergence, which makes the architecture readable without any of the three being open. Detail lives in `open-swe.md`; this entry exists so the names resolve.
- **WisePrax's anti-position:** these are the *build-it-yourself* answer, available only to firms with a platform team to spare. WisePrax's bet is that the same architecture, packaged as sovereign open-source infrastructure and running on local hardware, is what everyone else needs. Their existence is validation of the shape; their inaccessibility is the opening.

## What we learn from them

- **The market believes autonomous coding agents are real.** WisePrax doesn't need to argue the category exists; it argues only about the *shape* (sovereign, local, runtime-neutral, supervised).
- **The hosted SaaS shape is the default; the sovereign local shape is the open lane.** Devin, Copilot Workspace, and Cursor agents all chose hosted SaaS. The sovereign-local niche is largely unfilled at production-grade quality — that's WisePrax's lane.
- **Serious engineering orgs are building this in-house rather than buying it.** Stripe, Ramp, and Coinbase each built their own instead of adopting a vendor — evidence that the hosted-SaaS shape does not satisfy teams with real constraints, and that the demand is for infrastructure rather than for a product.
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
