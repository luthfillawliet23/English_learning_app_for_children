# Phase 1: "The Learning Hub"
### English Learning App — First Real Build

---

## 1. Application Description

**What it is:** The first real, multi-game version of the app. Instead of one standalone HTML file, this is a proper **hub** — a home screen he lands on, with tappable tiles for each game, a shared star counter, and a reward system that ties everything together. Phase 0's Word Family Slider gets folded in as the first game; **Sight Word Memory Match** joins it as the second.

**Why these two games together:** The slider is already validated — he's played it, engaged with it, and responded strongly to the picture + sound reveal moment. Sight Word Match leans directly into that same strength: it's almost entirely "flip, hear, reveal" — less hunting, more payoff, which matches what actually grabbed him in testing. Pairing a proven mechanic with one designed around his favorite part is a deliberate, low-risk way to expand.

**The experience:**
- **Home screen:** his avatar, a star count, and two large tappable tiles — "Word Families" and "Sight Words" — each opens its game in the same app, no separate downloads or links
- **Word Family Slider:** carried over from Phase 0 as-is, just re-skinned to live inside the hub instead of standing alone
- **Sight Word Memory Match:** a grid of face-down cards; tapping flips a card and speaks the word aloud; tapping its matching pair confirms the match with the same pop + reveal treatment as the slider, keeping the reward feel consistent across games
- **Shared stars:** every real word found or match made anywhere in the app adds to one running star total, shown on the home screen and inside each game
- **Bonus unlock:** after a set number of consecutive correct answers (in either game), a short, purely-fun animation or mini celebration unlocks — no teaching content, just a payoff, per the variable-reward principle from the framework
- **Quiet logging in the background:** every interaction (which word, which game, correct or not, when) gets saved locally on the iPad — invisible to him, but this is what lets you look at the data later and see exactly where he's strong or stuck

**What we're testing:** Does having a "menu" of two games increase engagement over a single game, or does it add friction for a 6-year-old? Does the sight word game's tighter reward loop hold his attention longer than the slider did on its own?

---

## 2. Technical Specification

| Item | Spec |
|---|---|
| **Platform** | Web app, iPad Air 5 Safari, touch-first, same as Phase 0 |
| **Delivery** | Hosted on GitHub Pages (already set up), single URL for the whole hub |
| **Structure** | One `index.html` hub shell + two game modules loaded into it (kept as separate JS/HTML sections, not separate pages, so navigation feels instant) |
| **Orientation** | Landscape preferred, functional in portrait |
| **Input** | Touch swipe (slider game), tap (memory match, navigation) |
| **Audio** | Web Speech API, same as Phase 0 |
| **Visuals** | Flat SVG illustrations — reuse the Phase 0 icon set for the slider; new set of sight-word-appropriate visuals not required since sight words (the, said, was) aren't picturable nouns — cards use bold text + color instead |
| **Storage** | `localStorage` for star count and per-word mastery status; structured as simple JSON objects, no backend |
| **Interaction log schema** | `{ game, word, correct, timestamp }` appended to a local array, capped at a reasonable size (e.g. last 500 events) to avoid bloating storage |
| **Session length target** | 3-5 minutes per sitting, matching the framework's guidance — the hub is designed for **repeatable short visits**, not one long session |
| **Accessibility** | Same large-touch-target and high-contrast standards as Phase 0 |
| **Out of scope for Phase 1** | Mic/speech-attempt tracking ("Read It, Say It"), full spaced-repetition adaptive engine, parent dashboard, guided tracing — all deferred to Phase 2+ |

---

## 3. Technical Architecture

```
┌───────────────────────────────────────────────┐
│              index.html (hub shell)              │
│                                                     │
│   ┌───────────────────────────────────────┐     │
│   │           Hub State (app-level)          │     │
│   │  - currentView: "home" | "slider" | "match" │
│   │  - totalStars                              │
│   │  - streak (for bonus unlock)                │
│   └───────────────┬───────────────────────┘     │
│                    │                                │
│      ┌─────────────┼─────────────┐                 │
│      ▼              ▼              ▼                 │
│  ┌─────────┐   ┌───────────┐  ┌───────────┐       │
│  │  Home    │   │  Word      │  │  Sight     │       │
│  │  Screen  │   │  Family    │  │  Word      │       │
│  │  (menu)  │   │  Slider    │  │  Match     │       │
│  └─────────┘   └───────────┘  └───────────┘       │
│                    │              │                    │
│                    └──────┬───────┘                    │
│                           ▼                             │
│                 ┌──────────────────┐                   │
│                 │   Reward Engine    │                   │
│                 │  (stars, streaks,   │                   │
│                 │   bonus unlocks)    │                   │
│                 └─────────┬────────┘                   │
│                           ▼                             │
│                 ┌──────────────────┐                   │
│                 │  Local Storage      │                   │
│                 │  (stars, mastery,   │                   │
│                 │   interaction log)  │                   │
│                 └──────────────────┘                   │
└───────────────────────────────────────────────┘
```

**Data structure (local storage):**
```json
{
  "totalStars": 14,
  "streak": 3,
  "wordMastery": {
    "cat": "learning",
    "the": "mastered"
  },
  "interactionLog": [
    { "game": "slider", "word": "cat", "correct": true, "timestamp": 1752500000 },
    { "game": "match", "word": "the", "correct": true, "timestamp": 1752500042 }
  ]
}
```

**Navigation flow:**
1. App loads → reads local storage → shows Home screen with current star count
2. Tap a game tile → `currentView` changes → that game's module renders in place (no page reload)
3. Any correct answer in any game → Reward Engine increments stars + streak, logs the interaction, checks if streak hits the bonus threshold
4. Bonus threshold hit → short celebration plays, streak resets, star total persists
5. Tap "Home" (small button inside each game) → returns to menu, stars/progress visible immediately

---

## 4. How I'll Build It — Step by Step

**Step 1 — Hub shell and navigation**
Build the home screen with the two game tiles and star display, plus a simple show/hide mechanism to switch between views without reloading the page. No games wired in yet — just confirm navigation feels instant and clear on the iPad.

**Step 2 — Port the Word Family Slider into the hub**
Move Phase 0's game into the hub as its own module, swapping its standalone star counter for the shared one. This should look and feel identical to what he already tested — no regressions.

**Step 3 — Build Sight Word Memory Match**
Card grid, flip logic, audio-on-flip, match detection, and the same pop/reveal treatment used in the slider so the payoff feels consistent across both games. Start with a small set of high-frequency words (the, said, was, you, are, my) — enough for a real session without overwhelming the grid.

**Step 4 — Wire up the shared Reward Engine**
Centralize star counting and streak tracking so both games feed into one system instead of each having its own. Add the bonus-unlock celebration for hitting a streak threshold.

**Step 5 — Add quiet interaction logging**
Every correct/incorrect event gets timestamped and saved locally. Nothing visible to him — this is purely for you to review later and see patterns (which words trip him up, which game he favors, session length trends).

**Step 6 — Full test on the actual iPad**
Same as Phase 0 — check touch responsiveness, audio, and navigation directly on his device before the real test, not just on a laptop.

**Step 7 — Ship and observe**
Hand him the iPad, let him choose which game to open himself (this alone is useful data — which tile does he pick first?), and watch without guiding.

**What I'll need from you after that:** which game he gravitated to, whether switching between games felt natural or confusing, and whether the shared star system felt meaningful to him or went unnoticed. That decides Phase 2 priorities — most likely "Read It, Say It" (the speech-practice layer) and Guided Trace.

---

*Next step: reply "build it" and I'll start on the hub shell first, then layer in the two games.*
