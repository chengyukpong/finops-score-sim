# AGENTS.md

## Project Overview

**finops-score-sim** — EC2 Optimization Score Simulator

Single-page HTML app that calculates an optimization score (0–100) for EC2 instances based on cost optimization and CPU right-sizing.

## Tech Stack

- Single `index.html` file — no build tools
- React 18 via CDN
- MUI v5 via CDN
- Babel standalone for JSX transpilation
- All inline CSS/JS

## Instance Types & Pricing

| Instance Type | On-Demand ($/hr) | Default CPU % |
|---|---|---|
| m8g.medium | 0.04488 | 80% |
| m8g.large | 0.08976 | 60% |
| m8g.xlarge | 0.17952 | 40% |

CPU % is derived from the target instance type (not user-editable). It represents the expected CPU utilization after right-sizing.

## Scoring Formula

RI and Scheduled Shutdown are **exclusive** — enabling RI disables and resets shutdown hours.

Final score = weighted combination of 2 active factors:

| Factor | Weight | When Active | Formula |
|---|---|---|---|
| RI Savings | 0.7 | RI Applied checked | 100 if applied, 0 if not |
| Idle Hours Coverage | 0.7 | RI not applied | `scheduledShutdown / detectedIdle × 100` |
| CPU Right-sizing | 0.3 | Always | CPU ≤ 60: `cpu/60×100`, CPU > 60: `max(0, 100-(cpu-60)/40×100)` |

- The cost optimization factor (70%) comes from RI **or** Shutdown, whichever is active
- CPU factor (30%) is always active
- Max achievable score is 100

### Score Color Mapping

| Range | Color | Label |
|---|---|---|
| 0–40 | Red (#f44336) | Poor/Very Poor |
| 40–70 | Orange (#ff9800) | Fair |
| 70–100 | Green (#4caf50) | Good/Excellent |

## Key UI Components

- `ScoreGauge` — SVG circular progress with color-coded score
- `FactorCard` — Reusable card showing factor score, weight, contribution
- Weekly Cost widget — shows base cost (current instance) vs optimized cost with savings
- Inputs: Current/Target instance type dropdowns, RI discount slider with checkbox, idle/shutdown hours fields, CPU indicator (read-only, synced to target instance type)

## Conventions

- Score values rounded to 1 decimal place (`Math.round(x * 10) / 10`)
- All MUI components destructured from `MaterialUI` global
- State managed with `React.useState` hooks
- Real-time recalculation on input change
- RI discount slider auto-calculates RI price from On-Demand price
- On-Demand price is read-only, driven by target instance type selection
- Coverage chip turns red when shutdown hours exceed detected idle hours (over 100%)

## Git

- Default branch: `master`
- GitHub: `chengyukpong/finops-score-sim`
