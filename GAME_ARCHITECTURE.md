# Hero Victor - Architecture Guide

## Overview
This repository now contains two playable HTML builds:
- `hero-victor.html` -> classic/legacy experience.
- `hero-victor1.html` -> new guided branching adventure.

Both builds are single-file, self-contained browser games using vanilla JavaScript, inline CSS, and inline SVG.

## File Layout
- `index.html`: landing/redirect page.
- `hero-victor.html`: classic gameplay.
- `hero-victor1.html`: enhanced gameplay (routes, power-ups, boss phases).
- `README.md`: user-facing overview.
- `GAME_ARCHITECTURE.md`: this reference.

## `hero-victor1.html` Core Systems

### Game Flow
1. Select difficulty.
2. Before each checkpoint, choose route (`safe` or `treasure`).
3. Answer a route-selected question.
4. Receive dynamic reward + optional event (coins/power-up).
5. Repeat until checkpoint target reached.
6. Enter 3-phase boss sequence.
7. Win by clearing boss phase 3 and opening chest.

### State Model (key fields)
- `hearts`, `answered`, `streak`, `total`, `correct`, `coins`
- `difficulty`, `questionsPerRound`
- `questions`, `usedQuestionKeys`
- `pathChoiceHistory`, `currentRoute`
- `inventory: { hints, shields, doubleCoins }`
- `pendingDoubleCoins`
- `inBoss`, `bossPhase`
- `busy`, `current`

### Route System
- Route chooser shown every checkpoint (`#route-chooser`).
- `chooseRoute(route)` stores choice and loads route-biased question.
- `pickQuestionByRoute(route)` filters by `pathAffinity` and falls back safely when needed.

### Question Difficulty Shaping
- Questions include metadata:
  - `pathAffinity`: `safe | treasure | mixed`
  - `rewardBand`: `safe | challenge | mystery`
  - `cognitiveLoad`
  - `distractorStrength`
- `loadQuestion()` rebuilds options with plausible distractors from similar questions to reduce obvious answers while staying age-appropriate.

### Rewards and Events
- `getCoinsForCorrectAnswer()` computes dynamic rewards from:
  - base reward by difficulty,
  - route multiplier,
  - reward band,
  - streak bonus,
  - completion bonus,
  - optional double-coins multiplier,
  - event bonuses.
- `getRouteEvent()` may grant:
  - extra coins,
  - hint,
  - shield,
  - double-coins token.

### Power-Ups
- UI: `#powerups` with `Hint`, `Double Coins`, and shield counter.
- `useHint()`: disables one wrong answer.
- `useDoubleCoins()`: arms multiplier for next correct answer.
- Shield is consumed automatically on wrong answer to prevent heart loss.

### Boss System
- `BOSS_PHASES` defines 3 boss questions per difficulty.
- `startBossMode()` enters boss state.
- `loadBossQuestion()` advances and renders each phase.
- `renderBossPhase()` updates `#boss-phase` indicator.
- Win only after final boss phase resolves.

### Keyboard Input
- Route navigation and selection.
- Answer navigation and submit.
- Power-up hotkeys (`H`, `X`).

## UI Areas (`hero-victor1.html`)
- World area: hero, pet, checkpoints, chest, hearts, coins, progress.
- Question panel additions:
  - `#boss-phase`
  - `#route-chooser`
  - `#powerups`
  - answer buttons + `#feedback`

## Deployment Notes
GitHub Pages serves both URLs independently:
- `/hero-victor/hero-victor.html` (classic)
- `/hero-victor/hero-victor1.html` (new)

## Debug Tips
- `console.log(state)` for current run internals.
- Verify route flow by checking `state.pathChoiceHistory` growth each checkpoint.
- Verify boss progression with `state.inBoss` and `state.bossPhase`.
