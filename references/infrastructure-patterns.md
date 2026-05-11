# Infrastructure pattern sources

> **Category:** projects that aren't competitors but whose architecture is worth borrowing for specific Praxant primitives.
> **All projects here:** OSI-approved licenses, well-documented architecture, large communities. Safe to study for patterns; no source code is copied.

## LiteLLM

- **License:** MIT (with some Enterprise features under a commercial license — the OSS core is MIT).
- **Pattern:** **multi-provider LLM API abstraction** with ~95% community-contributed providers. Single client interface, dozens of backend providers.
- **Praxant relevance:** the Model abstraction in `project-specs.md` §4 mirrors this. LiteLLM proves the pattern scales — and that the *interface design* is what determines whether community contributions are easy.
- **What to borrow:** the shape of a small, stable core interface plus a wide community-owned adapter surface. The lesson is interface discipline, not the code.

## Terraform (provider plugin architecture)

- **License:** MPL 2.0 (with the post-2023 BSL caveat on newer versions — older provider patterns predate that and remain widely used).
- **Pattern:** **plugin architecture with a community-owned adapter ecosystem.** Terraform's core is small; the providers (AWS, GCP, Azure, …) are separate packages that implement a defined interface and ship independently.
- **Praxant relevance:** Praxant's VCS, Chat, Agent, Model, and Secret abstractions follow the same shape. SP-2, SP-3, SP-4, SP-9 each ship one v1 adapter (Forgejo, Mattermost, Claude Code, OpenBao); additional adapters come on demand, owned by whoever needs them.
- **What to borrow:** the discipline of treating the core and the adapters as separately versioned, separately reviewed surfaces.

## Backstage

- **License:** Apache 2.0.
- **Pattern:** **extension points for plugin-rich platforms.** Backstage's plugin model lets organizations add internal-tool integrations without forking the core.
- **Praxant relevance:** less directly applicable to v0.1 (Praxant's adapters are typed Go interfaces, not Backstage-style frontend plugins), but a reference for how a platform stays extensible as adoption grows.

## Errbot

- **License:** GPLv3.
- **Pattern:** **multi-chat-backend abstraction.** Errbot supports Slack, Discord, IRC, XMPP, Mattermost, and others behind a single bot framework.
- **Praxant relevance:** SP-3 (chat provider abstraction) faces the same problem Errbot solved. The lesson is in *scope* — keep the common interface narrow (post, reply, react, listen), push provider-specific richness behind feature flags.
- **License caveat:** GPLv3 — patterns only, never copy code. Praxant is Apache 2.0; the licenses are not compatible in the direction we'd ever care about (copying GPL into Apache).

## Sweep AI (archived)

- **License:** Apache 2.0 — readable.
- **Status:** project is archived, but the code is preserved on GitHub.
- **Pattern:** **GitHub-PR-driven autonomous dispatch.** Issue → agent run → PR back, with the GitHub issue tracker as the orchestrating surface.
- **Praxant relevance:** the closest prior art to Praxant's "VCS-as-source-of-truth" stance. Sweep's death was a business outcome, not an architecture outcome — the dispatch shape is sound. Studying their code is fine since they're Apache 2.0 and archived.

## What ties these together

Each is a public, well-documented validation of one specific architectural primitive Praxant needs:

| Praxant primitive | Reference proves the shape works |
|---|---|
| Model abstraction | LiteLLM |
| Adapter ecosystem discipline | Terraform |
| Extension over time | Backstage |
| Chat abstraction scope | Errbot |
| VCS-driven dispatch | Sweep AI |

None of them are competitors. They are points in design space that already exist; Praxant is not asking whether these primitives can be built, only how to compose them for this product.
