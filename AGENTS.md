# AGENTS.md

## Project Overview

**finops-score-sim** — EC2 Optimization Score Simulator

Single-page HTML app that calculates an optimization score (0–100) for EC2 instances based on three factors.

## Tech Stack

- Single `index.html` file — no build tools
- React 18 via CDN
- MUI v5 via CDN
- Babel standalone for JSX transpilation
- All inline CSS/JS

## Scoring Formula

Final score = weighted combination of 3 factors:

| Factor | Weight | Formula |
|---|---|---|
| RI Savings | 0.4 | `(onDemand - riPrice) / onDemand × 100` (0 if RI not applied) |
| Idle Hours Coverage | 0.3 | `scheduledShutdown / detectedIdle × 100` |
| CPU Right-sizing | 0.3 | CPU ≤ 60: `cpu/60×100`, CPU > 60: `max(0, 100-(cpu-60)/40×100)` |

### Score Color Mapping

| Range | Color | Label |
|---|---|---|
| 0–40 | Red (#f44336) | Poor/Very Poor |
| 40–70 | Orange (#ff9800) | Fair |
| 70–100 | Green (#4caf50) | Good/Excellent |

## Key UI Components

- `ScoreGauge` — SVG circular progress with color-coded score
- `FactorCard` — Reusable card showing factor score, weight, contribution
- Inputs: RI pricing fields with checkbox, idle/shutdown hours fields, CPU slider

## Conventions

- Score values rounded to 1 decimal place (`Math.round(x * 10) / 10`)
- All MUI components destructured from `MaterialUI` global
- State managed with `React.useState` hooks
- Real-time recalculation on input change

## Git

- Default branch: `master`
- GitHub: `chengyukpong/finops-score-sim`
