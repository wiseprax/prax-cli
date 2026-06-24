# WisePrax logo concepts

Draft logo alternatives for **WisePrax** (= *wise* + *praxis* — "wise action":
supervised autonomous coding agents). Sources are vector SVG in `src/`;
`png/` holds 1024×1024 (wordmark 1600w) **transparent-background** PNG exports.

| File | Concept | Idea it carries |
|---|---|---|
| `01-owl` | Owl mark | Wisdom (Wise) + watchful human supervision |
| `02-praxis-loop` | Praxis loop | Cyclic agent action (Ralph Loop) around a supervised core |
| `03-constellation` | Orchestrator constellation | One supervisor + supervised praxagents, one active |
| `04-spark` | Wise spark (squircle) | Rising action (praxis) + insight; app-icon friendly |
| `05-hex-monogram` | Hex WP monogram | Self-hosted container + WisePrax initials |
| `06-wordmark` | Horizontal wordmark | Praxis-loop glyph + `WisePrax` lockup |

Palette: indigo `#4F46E5` / deep indigo `#312E81`, amber accent `#F59E0B`,
ink `#1E293B`.

## Regenerate PNGs from SVG

```sh
for f in 01-owl 02-praxis-loop 03-constellation 04-spark 05-hex-monogram; do
  inkscape "src/$f.svg" --export-type=png --export-filename="png/$f.png" -w 1024 -h 1024
done
inkscape src/06-wordmark.svg --export-type=png --export-filename=png/06-wordmark.png -w 1600
```

These are drafts, not a finalized identity. The `05-hex-monogram` and
`06-wordmark` marks render their text with DejaVu Sans Bold; convert text to
paths before any production use to make them font-independent.
