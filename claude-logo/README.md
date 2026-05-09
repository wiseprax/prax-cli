# Praxant Logo Concepts

Open **`preview.html`** in a browser. Everything is there: ten concept directions, each shown in
monochrome and color, on light and dark backgrounds, at full size and favicon scale, with full
rationale (description, why it works for Praxant, where to use, how it's built, strong points,
drawbacks) next to each visual.

## Files

```
logo/
├── preview.html                       ← start here
│
├── 01-node-trinity-{mono,color}.svg
├── 02-hidden-ant-network-{mono,color}.svg
├── 03-council-convergence-{mono,color}.svg
├── 04-praxant-wordmark-{mono,color}.svg     (viewBox 0 0 220 64 — wider)
├── 05-hex-colony-{mono,color}.svg
├── 06-ant-glyph-{mono,color}.svg
├── 07-orbital-agents-{mono,color}.svg
├── 08-praxis-pmark-{mono,color}.svg
├── 09-forge-anvil-{mono,color}.svg
└── 10-container-bracket-{mono,color}.svg
```

## Quick reference

| Token | Hex | Used for |
|---|---|---|
| Ink Navy | `#1A2B4A` | Primary mark color |
| Forge Copper | `#B45309` | Single accent on the focal element |
| Background | transparent | Never painted — host context provides |

- **Mono SVGs** use only `currentColor`. Recolor by setting CSS `color` on a parent element.
- **Color SVGs** use the hardcoded navy + copper palette.
- All SVGs are `viewBox`-based, scale infinitely, and total ~14 KB across 20 files.
