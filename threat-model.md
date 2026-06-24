# WisePrax — Threat Model

**Status:** Foundational architecture document
**Audience:** Contributors evaluating security-related changes; users deciding whether WisePrax fits their security requirements
**Date:** 2026-04-27

---

## 0. Why This Document Exists

WisePrax is a developer tool that runs on a developer's own machine and orchestrates AI coding agents on behalf of that developer. Its security primitives must match the actual threats a developer's machine faces — not the threats a datacenter, a public SaaS, or a regulated enterprise system faces.

Without an explicit threat model, every contributor brings their own assumptions, and the project drifts toward whichever assumption is loudest. Common drift directions:

- "We should require manual vault unseal" (enterprise-datacenter mindset applied to a workstation)
- "We need an HSM for secret storage" (compliance-theater applied to a personal dev tool)
- "We should encrypt secrets in transit between local processes" (network-attacker mindset applied to localhost)
- "We need full audit trails for every config change" (regulated-industry mindset applied to a single-user system)

**These aren't wrong in their original contexts.** They are wrong for WisePrax specifically. This document explains why, and provides the lens through which security proposals should be evaluated.

---

## 1. The Principle

> **WisePrax operates under the developer workstation threat model, not the enterprise datacenter threat model.**
>
> Security primitives must align with the realistic threats a developer's machine faces (backups, snapshots, file leaks, screenshots, casual logs, shared screens) — not with enterprise compliance theater (sealed vault ceremonies, separate-host key storage, hardware HSMs, multi-party authorization).
>
> **Friction that exists "because security" without addressing a realistic threat is rejected by design.**

This is non-negotiable. A contributor PR titled "Improve security by requiring manual unseal at startup" — or any equivalent enterprise-style hardening that breaks the developer experience without mitigating a realistic threat — should be closed with a reference to this document.

---

## 2. The Trust Boundary

WisePrax assumes a **single trusted host**: the developer's workstation, mini-PC, or homelab box where the orchestrator runs.

**Inside the trust boundary:**

- The WisePrax orchestrator binary
- The `wiseprax` CLI
- The OpenBao container (or `.env` file, depending on configured SecretProvider)
- The Postgres container
- Per-task praxagent containers
- The host filesystem
- The host's network stack
- The developer's browser (used for OAuth flows)
- The developer's local Docker socket
- Local Vaultwarden (if used as bootstrap source)

**Outside the trust boundary:**

- The internet at large
- Anthropic / OpenAI / Google / DeepSeek API endpoints (treated as untrusted external services)
- The Forgejo / GitHub / GitLab server hosting the source repos
- The Matrix (Tuwunel) / Mattermost / Slack server hosting team communication
- Any other host on the developer's LAN
- Backup destinations (cloud or otherwise)
- Other users of the same machine if multi-user

**The key point:** Anything inside the trust boundary is treated as if it has full access to anything else inside the trust boundary. This is realistic — a process running as your user account on your machine can already read your SSH keys, your browser cookies, your saved passwords, and your source code. Pretending otherwise creates security theater without security value.

---

## 3. Realistic Threats (What WisePrax Protects Against)

These are the threats a developer's workstation actually faces in 2026, and what WisePrax does about them.

### 3.1 Backup / snapshot / disk image leakage

**Threat:** A backup of the developer's machine ends up in insecure storage (cloud sync, lost USB drive, shared NAS, accidental upload).

**Mitigation:** Secrets are stored in OpenBao with encryption at rest. A leaked backup of the OpenBao data files is useless without the unseal key. The unseal key, if stored on the same host, is a separate file (`/etc/wiseprax/unseal.key` mode 0400) that the user is instructed to back up separately or accept the loss.

### 3.2 Accidental git commit of secrets

**Threat:** Developer accidentally commits a secret to a public or private repository.

**Mitigation:** Secrets never live in any file inside a git working directory. They live in OpenBao (or the configured SecretProvider). The `.env` fallback adapter writes to `~/.wiseprax/secrets.env`, which is outside any project directory and trivially `.gitignore`-able if the user keeps WisePrax config in a project. WisePrax's CLI never prints raw secret values to stdout in normal operation.

### 3.3 Application bug logging credentials to stdout/logs

**Threat:** A bug in WisePrax or a praxagent causes a secret to be written to log files where it could later be exposed.

**Mitigation:** Secrets are passed to praxagent containers via tmpfs-mounted files (mode 0400), not environment variables. The container's environment doesn't contain raw secrets. The orchestrator's logging is structured — secret values are tagged as sensitive and never serialized. Agents that need to use a secret read it from the file path provided in env vars (e.g., `FORGEJO_TOKEN_FILE=/run/secrets/forgejo_token`), not from a `FORGEJO_TOKEN=actual-secret-value` env var.

### 3.4 Screenshot / screen-share with secrets visible

**Threat:** Developer screen-shares or screenshots their terminal/UI while secrets are visible.

**Mitigation:** Secrets are never displayed in the WisePrax UI. The orchestrator dashboard shows references to secret keys (e.g., "Using anthropic.oauth_token from OpenBao") but never the values themselves. CLI commands like `wiseprax secrets list` show keys and metadata only, never values. To inspect a value, the user must explicitly run `wiseprax secrets show <key> --reveal`, which prints to stdout with a clear warning.

### 3.5 Stolen laptop with disk-encryption defeated

**Threat:** Laptop is stolen and the attacker bypasses full-disk encryption.

**Mitigation:** Partial. OpenBao encryption-at-rest provides a second layer. The unseal key (if stored on the same disk) is also accessible to the attacker, so this scenario degrades to "attacker has root on the machine" — at which point all bets are off (see §4.1). For developers in high-threat environments where this scenario matters, the recommended mitigation is **full-disk encryption with a strong passphrase** at the OS layer, not WisePrax-specific hardening.

### 3.6 Shared multi-user machine (low likelihood for the target audience)

**Threat:** Multiple users with separate accounts share the same physical machine; one user attempts to read another user's WisePrax secrets.

**Mitigation:** OpenBao runs as a Docker container owned by the user who started it. Filesystem permissions on the data directory and unseal key are 0400/0700 owned by the relevant user. A second user without root cannot read these files. (A second user WITH root can read everything; see §4.1.)

### 3.7 Network-listener attack against localhost services

**Threat:** Another process on the same machine listens on a port and intercepts credentials in transit between WisePrax components.

**Mitigation:** WisePrax components communicate over Docker network (which isolates from non-Docker host processes) and Unix sockets where possible. The Postgres container only accepts connections from inside the wiseprax Docker network by default. The OpenBao container similarly. Inter-container communication does not require additional encryption because the trust boundary already includes the host.

### 3.8 Compromised AI provider returning malicious responses

**Threat:** An AI provider (Anthropic, OpenAI, etc.) is compromised or hostile, and returns responses designed to manipulate the agent into doing harm.

**Mitigation:** Container isolation limits blast radius (the agent can only modify files within its own ephemeral container's git worktree). Default-deny network egress (allowlist only Anthropic/Forgejo/package mirrors) prevents the agent from exfiltrating data or contacting attacker infrastructure. Multi-model review council provides cross-checking — a malicious response from one provider is unlikely to be matched by independent providers. Automation policy can require human approval, additional review, or stricter quality gates for sensitive operations. **No single AI provider is trusted with unilateral authority.**

### 3.9 Compromised Forgejo / GitHub / GitLab server

**Threat:** The VCS server is compromised; attacker pushes malicious commits or modifies issues.

**Mitigation:** WisePrax treats the VCS as authoritative for source code but not for execution policy. Praxagents only run when explicitly allowed by automation policy after webhook/event intake. A malicious commit doesn't automatically get universal authority. Push access is gated by the developer's own VCS credentials, not WisePrax's. WisePrax cannot make the VCS more or less compromised than it would be without WisePrax — the attack surface is the developer's repo, not WisePrax.

### 3.10 Anthropic / OAuth token expiration without backup

**Threat:** The CLAUDE_CODE_OAUTH_TOKEN expires; if the developer hasn't backed it up, agents stop working until re-auth.

**Mitigation:** WisePrax tracks token expiration and warns 30 days before expiry via Matrix notification + dashboard banner. Tokens are stored in OpenBao with replication-friendly export options. The `wiseprax auth refresh <provider>` command provides a smooth re-auth flow.

---

## 4. Threats WisePrax Explicitly Does NOT Protect Against

Being explicit about what's out of scope is more honest than vague disclaimers. If any of these threats are in your real model, **deploy WisePrax differently or use a different tool**.

### 4.1 Attacker with root access on the host

If an attacker has root or full user-account access on the machine where WisePrax runs, they have:

- Your SSH private keys
- Your browser cookies (every site you're logged into)
- Your saved passwords (browser, system keyring)
- Your GPG keys
- Your AWS / GCP / Azure credentials
- Your source code
- Your shell history (every command you've ever run)
- Your `~/.config/` directory (everything)

In this scenario, WisePrax secrets being "extra-protected" is meaningless — the attacker already owns your digital identity. **No realistic WisePrax security feature can change this.** Manual vault unseal, hardware HSMs, multi-party authorization — all defeated by an attacker who can keylog your unseal ceremony.

The correct mitigation is at the layer below: full-disk encryption, OS hardening, physical security, not running untrusted code as your user account, and so on. **These are out of scope for WisePrax.**

### 4.2 Insider threat from co-maintainers or contributors

WisePrax is OSS. Contributors can submit PRs that, if merged, could include backdoors or data-exfiltration code. WisePrax does not technically prevent this. Mitigation is **process**: code review, contribution guidelines, signed commits (DCO), reproducible builds, eventually a security-focused code-of-conduct. These are out of scope for technical threat-model purposes; see CONTRIBUTING.md and SECURITY.md instead.

### 4.3 Supply-chain attacks against dependencies

If a Go module, Python package, npm package, or Docker base image used by WisePrax is compromised upstream, WisePrax inherits the compromise. Mitigation:

- Pin all dependencies to specific versions (no `:latest`, no floating versions)
- Verify checksums where supported (`go.sum`, `package-lock.json`, `Pipfile.lock`)
- Periodic audit of transitive dependencies
- Minimal dependency surface (don't pull in libraries we don't need)

But WisePrax cannot guarantee its dependencies are not compromised. **No tool can.** This is a project-wide concern shared with every modern software project.

### 4.4 Compliance-grade audit logging

WisePrax logs operationally relevant events but does not produce SOC2/HIPAA/PCI-DSS-grade tamper-evident audit trails. If you need:

- Cryptographically signed audit logs
- Append-only log storage
- Long-term log retention with chain-of-custody
- Multi-party authorization workflows
- Separation-of-duties enforcement

— WisePrax is not the right tool. Use a regulated-industry-grade orchestrator (typically commercial, often cloud-only) instead.

### 4.5 Multi-tenant isolation

WisePrax assumes a single user (or single trusted team) per deployment. It does not provide isolation between tenants on the same WisePrax instance. If you need to run "WisePrax as a service" for multiple unrelated organizations, deploy separate WisePrax instances per tenant — do not multi-tenant them.

### 4.6 Defense against quantum computers

WisePrax uses standard cryptography (TLS, modern symmetric ciphers, current public-key algorithms). It is not post-quantum-cryptography hardened. When the cryptographic community migrates to PQC, WisePrax will follow — but ahead-of-curve PQC migration is out of scope.

### 4.7 Defense against state-level adversaries with physical access

Cold-boot attacks, RAM extraction, evil-maid attacks, hardware implants — out of scope. The mitigation for these threats is air-gapped hardware in physically secured facilities, not application-level features.

### 4.8 Defense against the developer themselves

If the developer running WisePrax intends to leak their own secrets, exfiltrate their own data, or run malicious code on their own machine, WisePrax cannot stop them. This includes "developer ran a malicious VS Code extension that read OpenBao secrets via the `wiseprax` CLI" — WisePrax cannot distinguish a malicious extension from a legitimate one running with the developer's permissions.

---

## 5. Specific Design Decisions and Their Rationale

### 5.1 OpenBao auto-unseal at host startup

**Decision:** OpenBao's unseal key is stored on the same host as OpenBao itself, and OpenBao auto-unseals when the host boots.

**Rationale:**

- The realistic threat is encrypted-data-at-rest in backups/snapshots — auto-unseal still protects against this.
- The unrealistic-for-this-context threat is "attacker compromises OpenBao container but not the host" — this scenario doesn't happen on a single-host developer workstation; if the host is compromised, OpenBao is compromised.
- Manual unseal would require the developer to enter the unseal key after every reboot. After 3 reboots, the developer writes the key on a sticky note. Net result: SAME security, WORSE UX.
- Enterprise vault deployments use distributed unseal keys because their threat model includes "Vault host compromise without datacenter compromise." That model doesn't apply here.

**Counter-argument someone might raise:** "But isn't auto-unseal less secure?"
**Response:** Less secure against a threat that doesn't apply to this deployment model. Manual unseal would be theater, not security. See §1.

### 5.2 Secrets delivered to praxagent containers as tmpfs-mounted files (not env vars)

**Decision:** Per-task secrets are written to `/run/secrets/<name>` (tmpfs mount, mode 0400) inside the container. Tools that require env-var auth read the file value into env scope at the moment of the subprocess call.

**Rationale:**

- Env vars leak via `/proc/<pid>/environ`, child process inheritance, crash dumps, observability tools, `ps -E` output.
- Files do not have these leak vectors.
- tmpfs ensures secrets never touch disk inside the container.
- Per-task scope means secret cleanup is automatic (container exit destroys the tmpfs).
- This matches Kubernetes Secrets, Docker Swarm Secrets, HashiCorp Vault Agent, and systemd LoadCredential — the modern consensus.

### 5.3 Default-deny network egress from praxagent containers

**Decision:** Praxagent containers can only reach an explicit allowlist of network destinations (Anthropic API, Forgejo, package mirrors). All other egress is blocked.

**Rationale:**

- A confused or compromised agent cannot exfiltrate data to attacker infrastructure if it cannot reach the internet generally.
- A confused or compromised agent cannot pull arbitrary code from random sources mid-execution.
- The single highest-leverage security control for autonomous agent execution.
- The performance cost is zero; the operational cost is maintaining the allowlist (manageable).

### 5.4 No sudo / no root inside praxagent containers

**Decision:** Praxagent containers run as a non-root user. Root capabilities are dropped at container startup. No sudo binary is present.

**Rationale:**

- Limits damage from agent mistakes (can't `rm -rf /` because /root is not writable).
- Limits container escape attack surface (most container escapes require root in the container).
- Standard hardening pattern; no operational cost.

### 5.5 Container-level resource limits (CPU, memory, disk)

**Decision:** Each praxagent container has explicit cgroup limits on CPU, memory, and disk usage.

**Rationale:**

- Prevents a runaway agent from starving the host.
- Prevents a confused agent from filling the disk with logs or generated files.
- Operational hygiene; no security-specific reason but aligns with the broader "bounded-blast-radius" pattern.

### 5.6 Automation policy gate before agent dispatch or merge

**Decision:** WisePrax evaluates automation policy before sensitive workflow transitions such as task dispatch and merge. Depending on configuration, that policy may require human approval, review-council consensus, deterministic quality gates, or some combination.

**Rationale:**

- Supports teams with different trust models instead of forcing one doctrine.
- Provides a safe default for early deployments while allowing policy-authorized progression later.
- Lets high-risk changes require human review while low-risk changes can advance through deterministic gates.
- Creates a clear place to encode escalation rules, merge rules, and quality thresholds.

This is **not** a defense against compromised humans or bad policy configuration. It is a control point for limiting autonomous-system runaway and for calibrating trust based on repository policy.

### 5.7 No raw secret values in WisePrax logs, UI, or CLI output

**Decision:** Secret values never appear in operator-visible surfaces. References are by key name (e.g., "anthropic.oauth_token") only. To see a value, explicit `wiseprax secrets show <key> --reveal` is required.

**Rationale:**

- Screen-sharing, screenshot, log forwarding, observability tools all become safe by default.
- Eliminates an entire class of accidental disclosure.

---

## 6. How Contributors Should Evaluate Security Proposals

When considering a security-related PR, plugin, or feature request, walk through these questions:

1. **What is the realistic threat being mitigated?** Articulate it concretely. "Hardening" is not a threat. "Defense in depth" is not a threat. "An attacker who has root on the host wants to read OpenBao secrets" is a (non-)threat — see §4.1.

2. **Is the threat in scope per §3, or out of scope per §4?** If out of scope, the change does not belong in WisePrax core. (It might belong in deployment documentation, in a separate hardened-deployment guide, or in a third-party plugin.)

3. **Does the proposed change add operational friction for the developer?** If yes, the friction must be justified by the realistic threat. Friction without realistic mitigation is rejected (see §1).

4. **Does the change introduce enterprise-style assumptions** (compliance audit logs, multi-party authorization, distributed key ceremonies, HSM requirements)? If yes, default to "no, not in core." WisePrax is not an enterprise-compliance product.

5. **Could the change be implemented as an optional plugin or alternate adapter** instead of a core requirement? If yes, that's the right shape — keep WisePrax core focused on the developer-workstation threat model and let users who need more deploy plugins/alternatives.

A useful template for evaluating proposals:

> "This PR adds [feature]. The realistic threat it mitigates is [specific threat from §3, OR from a documented extension to §3]. The operational cost to the developer is [specific friction]. The cost is justified because [reason]. If the cost is not justified or the threat is in §4, this PR is closed with reference to THREAT_MODEL.md."

---

## 7. How Users Should Evaluate Whether WisePrax Fits Their Threat Model

If you're a developer evaluating WisePrax for your own use:

| Your situation | WisePrax fit |
|---|---|
| Solo developer working on personal projects | ✓ Excellent fit |
| Small team (3–10) on shared self-hosted infrastructure | ✓ Good fit |
| Small/medium business with own datacenter | ✓ Good fit; review §4 to confirm no compliance gaps |
| Sovereign-tech / EU regulated industry / sovereignty-conscious organization | ✓ Good fit; aligns with positioning |
| Government / defense with classification requirements | ⚠ Probably needs additional hardening; evaluate §3 vs your specific requirements |
| Banking / healthcare with formal SOC2/HIPAA/PCI requirements | ✗ Not a compliance-grade tool — see §4.4. Use a regulated-industry orchestrator. |
| Multi-tenant SaaS hosting agents for unrelated customers | ✗ Not designed for this — see §4.5. Deploy separate instances per tenant or use a different tool. |
| Air-gapped classified environments | ⚠ Possible with extra effort; some external dependencies (model providers) require internet by default |

If your situation is in the ⚠ or ✗ rows, **don't try to "harden WisePrax" until it fits**. Use a tool designed for your threat model in the first place. WisePrax is open-source — you can fork it and adapt — but the upstream project's scope is the developer-workstation threat model.

---

## 8. Reporting Security Issues

For genuine security vulnerabilities affecting in-scope threats (§3), please follow the process in `SECURITY.md` (will be added during SP-B repo bootstrap).

Out-of-scope concerns (§4) and "defense in depth" suggestions without specific threats are welcomed as discussion topics on the project's discussion forum but should not be filed as security vulnerabilities.

---

## 9. Document Maintenance

This threat model is **versioned** with the project. As WisePrax evolves and the threat landscape changes, this document is updated. Significant changes require a PR with rationale, reviewed against the principle in §1.

The current version reflects the v1 architecture. Future versions may expand scope (e.g., add multi-tenant isolation if that becomes a v2 goal), but the core principle — developer-workstation threat model, no security theater — is intended to be permanent.

---

*The single most important sentence in this document is in §1. If a proposed change conflicts with that sentence, the change is wrong, not the sentence.*
