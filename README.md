# Hero's Quest

## Play
- Main page: https://dienomb.github.io/hero-victor/
- Classic version: https://dienomb.github.io/hero-victor/hero-victor.html
- New interactive version: https://dienomb.github.io/hero-victor/hero-victor1.html

## Versions
- `hero-victor.html`: classic trivia run (legacy flow).
- `hero-victor1.html`: guided branching adventure with richer interactions.

## New Mechanics in `hero-victor1.html`
- Checkpoint-by-checkpoint route choice:
  - `Safe Trail`: steadier rewards, lower risk.
  - `Treasure Trail`: higher rewards, more risk.
- Variable rewards per question based on:
  - difficulty,
  - route,
  - reward band,
  - streak,
  - event roll.
- Power-ups:
  - `Hint`: removes one wrong answer,
  - `Shield`: blocks one heart loss,
  - `Double Coins`: doubles next correct reward.
- 3-phase final boss sequence with boss phase indicator.
- Harder question experience at same kid-friendly level:
  - less obvious distractors,
  - more reasoning-focused options.

## Controls
- Answers: `1/2/3`, `A/B/C`, arrow keys + Enter.
- Route choice: `1` or `A` for Safe, `2` or `D` for Treasure, arrows + Enter.
- Power-ups: `H` (Hint), `X` (Double Coins).

## Tech
- Vanilla HTML/CSS/JavaScript.
- Inline SVG graphics.
- Web Audio API sound effects.
- No external runtime dependencies.

## Local Run
- Open `hero-victor.html` for classic mode.
- Open `hero-victor1.html` for the new interactive mode.

## Repository
- https://github.com/dienomb/hero-victor
