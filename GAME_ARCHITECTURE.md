# Hero Victor - Game Architecture Guide

## Quick Overview
Hero Victor is a single-file, self-contained educational trivia game. Everything is in `hero-victor.html` - no external dependencies, frameworks, or separate asset files.

**Key Facts:**
- **File Structure:** Just `index.html` (redirect) and `hero-victor.html` (main game)
- **Game Type:** Educational trivia/quiz adventure for children
- **Architecture:** Pure vanilla JavaScript + inline CSS + inline SVG graphics
- **Deployment:** Perfect for GitHub Pages

---

## Game Mechanics at a Glance

### Goal
Guide a hero character across a road by answering 5 trivia questions correctly, then answer a final "boss question" to open the treasure chest and win.

### Flow
1. **Start** → Hero at position 0
2. **Answer Question 1-5** → Correct = hero moves forward to checkpoint, wrong = lose 1 heart
3. **Answer Boss Question** → Correct = open chest & win, wrong = lose 1 heart
4. **Game Over** at 0 hearts remaining (lose) OR after opening chest (win)

### Restart
Player can restart by clicking the restart button after win/lose screens

---

## Code Structure

### State Management
```javascript
state = {
  hearts: 5,           // Remaining lives (start with 5)
  answered: 0,         // Number of checkpoints completed (0-5)
  questions: [],       // Shuffled questions for this playthrough (5 questions)
  finalMode: false,    // Flag: currently on the boss question?
  busy: false,         // Flag: is a transition/animation in progress?
  current: null        // Current question object being displayed
}
```

### Main Game Functions (in execution order)

#### Initialization
- **`initGame()`** - Called on page load
  - Resets state to starting values
  - Shuffles questions array
  - Loads first question
  - Shows game UI

#### Question Loading
- **`loadQuestion()`** - Loads next question from `state.questions` or enters final mode
  - Updates `state.current` with new question
  - If `state.answered === 5`, switch to final mode (boss question)
  - Updates UI with question text and answer buttons

#### User Interaction
- **`answer(index)`** - Called when player clicks answer button
  - `index` = which button clicked (0, 1, or 2)
  - Checks if `index === state.current.ok` (correct answer index)
  - **If correct:** Moves hero, lights checkpoint, loads next question
  - **If wrong:** Shakes hero, loses 1 heart, reloads same question
  - Prevents interaction while busy (`state.busy` flag)

#### Hero Movement
- **`moveHero()`** - Animates hero from current position to next position
  - Uses `STOPS` array to determine positions: `[50, 200, 350, 500, 650, 800, 880]`
  - CSS transition handles animation (0.9s cubic-bezier)
  - Increments `state.answered`
  - Lights up corresponding checkpoint star

#### Visual Feedback
- **`flash(color, duration)`** - Flash the entire question panel
  - Green = correct answer
  - Red = wrong answer
- **`shakeHero()`** - Wiggle animation when wrong
- **`animateConfetti()`** - Particle effect on win (55 particles, multiple colors)
- **`celebrate()`** - Hero celebration jump animation

#### Win/Lose Conditions
- **`checkWin()`** - Checks if both conditions met:
  1. `state.answered === 5` (completed all checkpoints)
  2. Just answered the boss question correctly
- **`checkLose()`** - Checks if `state.hearts === 0`

---

## Question Data Structure

### Format
```javascript
{
  type: "Math ➕",                    // Category with emoji
  q: "What is 5 + 3?",              // Question text
  a: ["7", "8", "9"],               // Three answer options (A, B, C)
  ok: 1                             // Index of correct answer (0, 1, or 2)
}
```

### Question Pool
- **Math Questions:** 12 total (arithmetic, geometry, counting)
- **Science Questions:** 12 total (biology, physics, Earth science)
- **Total:** 24 questions
- **Per Game:** 5 random questions selected + 1 boss question

### Key Questions Arrays
- **`questions`** - All 24 available questions (defined in code)
- **`state.questions`** - Shuffled subset (5 questions for current playthrough)
- **`CHEST_QUESTION`** - The special boss question asked after 5 checkpoints

---

## DOM Elements & CSS Classes

### Game Container
- **ID:** `#gameContainer` (900px × 580px)
- **Layout:** Two sections (60% world, 40% questions)

### World Section (Upper)
- **ID:** `#world` - Game canvas area
- **`#hero`** - SVG hero character (positioned via `left` CSS property)
  - Positions: `left: 50px, 200px, 350px, 500px, 650px, 800px, 880px`
  - Animation class: `.walk` (moves hero with bob animation)
- **`#chest`** - SVG treasure chest (right side)
  - Animation class: `.open` (opens on win)
- **`#clouds`** - Animated background clouds
- **`.checkpoint`** - Star elements (5 total)
  - ID: `#check1` through `#check5`
  - Animation class: `.active` (glows when passed)
- **`#lifeDisplay`** - Heart counter (shows remaining hearts)

### Question Section (Lower)
- **ID:** `#questionPanel` - Question UI container
- **`#questionType`** - Category label (e.g., "Math ➕")
- **`#questionText`** - Question text display
- **`.answerBtn`** - Answer buttons (3 total)
  - IDs: `#btn0`, `#btn1`, `#btn2`
  - Content: "A", "B", "C" with option text

### Game Over Screens
- **ID:** `#winScreen` - Hidden until win condition met
  - Contains restart button
- **ID:** `#loseScreen`** - Hidden until lose condition met
  - Contains restart button

---

## Key CSS Animations

### Hero Animations
- **`.walk`** - Moves hero with bobbing motion (0.9s)
  - Used during `moveHero()`
- **`.shake`** - Wiggle animation (0.5s)
  - Used on wrong answer in `shakeHero()`
- **`.celebrate`** - Jump/celebration animation
  - Used on final correct answer in `celebrate()`

### Checkpoint Animations
- **`.active`** - Glow effect (star lights up)
  - Applied after hero passes checkpoint

### Visual Feedback
- **`.flash.correct`** - Green background flash (0.3s)
- **`.flash.wrong`** - Red background flash (0.3s)

### Confetti
- **`.confetti`** - Falling particle animation (2-4s)
  - Generated dynamically in `animateConfetti()`
  - Multiple colors (random from color array)

---

## Event Listeners

### Initialization
- **`window.addEventListener('load', initGame)`** - Start game on page load

### User Interaction
- **Button clicks:** `.answerBtn` elements trigger `answer(0/1/2)`
- **Restart buttons:** Trigger `location.reload()` to reset game

---

## Visual Layout

```
┌─────────────────────────────────────────┐
│          WORLD (60%)                    │
│  ☁️ ☁️                                   │
│  🌳━━━⭐━━━⭐━━━⭐━━━⭐━━━⭐━━━🏆       │
│      🧑                                  │
│  ❤️❤️❤️❤️❤️  Progress: 1/6              │
├─────────────────────────────────────────┤
│       QUESTIONS (40%)                   │
│  Math ➕                                 │
│  What is 5 + 3?                        │
│  [ A: 7 ] [ B: 8 ] [ C: 9 ]           │
└─────────────────────────────────────────┘
```

---

## Recent Improvements (Interactive Version)

### ✅ Keyboard Support
- **Number Keys:** Press `1`, `2`, or `3` to select answers directly
- **Letter Keys:** Press `A`, `B`, or `C` to answer
- **Arrow Keys:** Use left/right arrows to navigate between buttons
- **Enter Key:** Press Enter to confirm selected answer with visual outline

### ✅ Enhanced Visual Feedback
- **Button Animations:**
  - Hover: lift up with shadow effect
  - Click: responsive press animation
  - Flash: brighter, more pronounced feedback (correct/wrong)
- **Button Focus States:** White outline when keyboard-navigated
- **Hero Animations:** Enhanced celebration jump with rotation
- **Question Panel:** Smooth fade-in animations
- **Interactive Transitions:** Smooth state changes with CSS animations

### ✅ Score Tracking & Statistics
- **Streak Counter:** 🔥 Displays consecutive correct answers
  - Green at 0-2
  - Gold/orange at 3+ (hot streak!)
  - Resets on wrong answer
- **Accuracy Tracking:**
  - Total answers counted
  - Correct answers tracked
  - Accuracy percentage calculated
  - Shown on win/lose screens
- **Game Stats Display:** Shows performance metrics after each game

### ✅ Answer Feedback Messages
- **Correct Feedback:** Green "✅ Correct! Great job!" message
- **Wrong Feedback:** Red message showing correct answer (e.g., "❌ Wrong! The correct answer is B: 8")
- **Instant Learning:** Feedback clears when loading next question
- **Smooth Animations:** Messages fade in smoothly

## Remaining Enhancement Opportunities

### Potential Future Improvements
1. **Sound Effects** - Correct/wrong answer sounds, level up effects
2. **Difficulty Modes** - Easy/Normal/Hard with different question pools
3. **Hint System** - Reduce hearts to get a hint
4. **Leaderboard** - Track best scores/times
5. **Accessibility** - Screen reader support, better keyboard nav
6. **Mobile Touch** - Better mobile/touch interface
7. **Question Categories** - Let players choose which categories to answer
8. **Power-ups** - Extra hearts, answer revealed, etc.
9. **Timed Questions** - Countdown timer for additional challenge
10. **Custom Questions** - Allow users to add their own questions

---

## Debugging Tips

### Check Game State
- Open browser console and type: `console.log(state)`
- Check current question: `state.current`
- Check hearts: `state.hearts`
- Check progress: `state.answered`

### Common Issues
- **Hero not moving:** Check `state.busy` flag and animation completion
- **Questions not loading:** Check `state.questions` array is populated
- **Buttons not responding:** Check `state.busy` flag during animations
- **Win/lose not triggering:** Verify both conditions in `checkWin()` and `checkLose()`

---

## File Sizes
- **index.html:** 149 bytes (redirect)
- **hero-victor.html:** ~21,668 bytes (complete game)
- **Total:** ~22 KB (single file deployment)

---

## Next Session Checklist
When returning to improve the game:
1. Read this guide first (skip full code review)
2. Use function names and locations in this guide to navigate
3. Identify specific functions to modify for improvements
4. Make one improvement per commit for clarity
5. Update this guide with any new functions or major changes
