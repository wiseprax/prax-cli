# The "sell work, not software" debate (Sequoia vs. the commoditization rebuttal)

> **Type:** strategic argument, not a project. Nothing here is prior art to borrow code or architecture from — this file exists because the debate directly stress-tests the WisePrax business posture (platform as internal delivery engine, never sold as a product).
> **Primary source:** Guillermo Flor, *"Sequoia is wrong — services aren't the opportunity"* — https://www.theaiopportunities.com/p/sequoia-is-wrong-services-arent-the (endorsing Andrew Côté's rebuttal of Sequoia's thesis).
> **Status:** v1, 2026-08-04. Positions summarized in good faith, then argued with. Read §3 before §5 — the critique of the critique is the operative part.

## 1. The two positions

**Sequoia's thesis: "sell work, not software."**
The next trillion-dollar AI companies capture value from the global *labor* budget rather than the software licensing budget. Instead of selling a tool that helps someone do the work, you complete the work and take a margin on it. The claim is fundamentally about **TAM shape**: labor is a vastly larger pool than software licenses.

**The rebuttal (Côté, endorsed by Flor): services train their own replacement.**
Three moves:

1. *Commoditization.* "Using AI for a task trains AI to do that task, so every services niche eventually becomes a niche the model owns outright." The service layer is transient; the foundation model absorbs it.
2. *Scale mismatch.* Software is ~2% of the economy. Five physical sectors — chemicals, metals, energy, transportation, healthcare — each turn ~$10T/yr. The real prize is atoms.
3. *Economics unlock.* Physical industries are price-elastic: when AI and robotics cut cost, demand *expands* rather than merely reallocating share. Edge-deployable perception models are claimed to yield software-like margins (80%+ gross) on processes that today need labor in proportion to output.

Conclusion offered: own physical assets or regulated positions in atoms-based industries; avoid pure AI service wrappers, which are training data for their own obsolescence. Cited instances: Travis Kalanick's ATOMS (factories, supply chains); defense and energy already funding physical autonomy directly.

## 2. Why this file exists

WisePrax's operating posture is that the platform is the **delivery engine for a services business, not a product to be sold**. That posture is precisely the thing the rebuttal claims is doomed. This doc puts the counter-argument on record and names the risks that are actually load-bearing — which are real, but different from the ones the article states.

## 3. Where the rebuttal is weak

**3.1 "Using AI for a task trains AI to do that task" is false as literally stated.**
Inference against a hosted model does not update that model's weights. Whether provider traffic becomes training data is a *contractual* question — zero-retention terms exist and are standard on enterprise tiers. The genuine leak is thinner: diffusion of the *knowledge that task X is automatable*, plus publicly shared prompt/workflow patterns, plus staff mobility. Real, but ordinary competitive diffusion — not a mechanism by which a services firm uniquely manufactures its own successor.

**3.2 It conflates capability with position.**
Services moats were never the capability. They are distribution, trust, liability absorption, licensure, integration depth, switching cost, and data that legally cannot leave the client. Spreadsheets became excellent; the Big Four grew. When a capability commoditizes it commoditizes *for incumbents too* — a tailwind for a challenger services firm with a lower cost structure, not a headwind.

**3.3 It proves too much.**
The same argument dissolves the physical-AI thesis it advocates. Every warehouse-robot deployment generates perception data, diffuses the knowledge that the task is automatable, and gets replicated by competitors. The article exempts atoms without justifying the exemption — the real difference is **capital intensity as a moat**, not immunity from diffusion. That is a defensible claim, but it is a different claim, and it should be stated as "capex is the barrier," not "services are structurally doomed."

**3.4 The "software is 2% of the economy" statistic argues against the wrong target.**
Services are not software. Comparing $10T physical sectors against the software TAM does not indict services — services *are* the labor budget Sequoia is pointing at, and they are larger than software by an order of magnitude. The evidence supports an atoms-vs-bits argument dressed up as a services-vs-product argument.

**3.5 The two sides talk past each other.**
Sequoia's claim is about market size. The rebuttal is about defensibility. Both can be true simultaneously: enormous market, thin moats. Nothing in the rebuttal actually contradicts the TAM claim.

**3.6 The margin claim is asserted, not demonstrated.**
"80%+ gross margins from edge perception" has no working shown. Owning atoms means low ROIC, long capital cycles, working-capital drag, and regulatory exposure. ATOMS is a bet in flight, not evidence.

## 4. Where the rebuttal is right (and where the real risk lives)

**4.1 Wrapper services with no proprietary loop do get eaten.**
A firm with no accumulating data asset, no regulated position, and no switching cost is a staffing agency with better tooling. Margin compresses toward (labor arbitrage − token cost), and both terms move against you over time.

**4.2 Price-anchor deflation is the actual threat, and the article underrates it.**
If pricing is anchored on "labor replaced," and the labor cost of that task trends toward zero, the anchor collapses — not because the model learned from you, but because the *client* can now price-check you cheaply. Sharpest true point in the debate, and it is made only glancingly.

**4.3 "We run agents faster" is not a position.**
A tooling speed advantage diffuses on roughly the release cadence of the underlying commercial runtimes. Any pitch whose load-bearing claim is orchestration quality is exposed on that clock.

## 5. What this implies for WisePrax

The platform-as-internal-delivery-engine posture lands on the *right* side of §4.1 — but only if the moat is located correctly. The engine is not the moat; the engine is diffusion-exposed on the runtime release cadence (§4.3).

Defensible positions, in priority order:

1. **Delivery-loop data.** Per-vertical artifacts, failure taxonomies, and domain priors accumulated across engagements that a fresh entrant cannot purchase. The one asset that compounds with delivery volume.
2. **Liability and regulated position.** WisePrax signs for the outcome; a model cannot sign. Wherever a deliverable requires an accountable party, improving model capability does not remove the need for that party.
3. **Outcome pricing with embedded switching cost.** Price per unit of result, embedded in the client's system of record — *not* hourly, and specifically **not** "percentage of labor saved," which walks directly into the §4.2 deflation trap.

**Anti-patterns this debate rules out:**

- Pitching orchestration speed or agent-fleet size as the differentiator.
- Pricing indexed to displaced human labor cost.
- Any engagement shape that leaves no residual data asset with WisePrax after delivery.

**Consistency with existing tenets.** None of the above conflicts with substrate-not-behavior (`CLAUDE.md`): the platform stays generic, and the accumulating asset lives in skills, agent configurations, and per-vertical delivery artifacts — exactly where that tenet says behavior belongs. The moat and the substrate are deliberately different layers.

## 6. Open item

The services posture (platform as delivery engine, never sold) is **not yet reflected in the charter**. As of 2026-08-04, grep for "services", "delivery engine", and "consultancy" returns nothing in `project-specs.md`, `why-wiseprax.md`, or `README.md` — those documents still read as an open-source-product story. That gap is pre-existing and is not resolved by this file. When the charter is updated, §5 above is the argument that update should survive, and the change belongs in `project-specs.md` §11 with a date.

## 7. Cross-references

- `COMPARISON.md` §4 — "why WisePrax is special." Those are *product* differentiators; §5 above is the *business* differentiator. Not the same list, and they should not be merged.
- `CLAUDE.md` → "Substrate, not behavior" — see the §5 consistency note.
- `project-specs.md` §11 — where any charter reversal following from this argument must be logged.
