# Phase 0 Prototype: "Word Family Slider"
### English Learning App — Validation Build

---

## 1. Application Description

**What it is:** A single-screen, single-game interactive prototype — the **Onset-Rime Slider** ("Word Family Slider") from our roadmap. This is the *smallest possible thing* that can tell us whether the core game mechanic actually engages your son, before any investment in the hub, reward system, or other five games.

**Why this game, first:** Of the six planned games, this one is the most novel mechanic (nothing like his Kumon worksheets), the easiest to build in isolation, and the clearest test of engagement — either he wants to keep swiping to find new words, or he doesn't.

**The experience:**
- A fixed word ending ("rime") sits on the right side of the screen, e.g. `-at`
- A single letter ("onset") sits on the left, inside a vertical slider he can swipe up/down
- As he swipes, the letter cycles (c, b, m, s, h, r...) and the full word updates live: `cat`, `bat`, `mat`, `sat`, `hat`, `rat`
- When the combination forms a **real word**, the app:
  - Plays a short celebratory animation (the letter "pops" or bounces)
  - Reads the word aloud automatically
  - Shows a matching picture (a cat, a bat, a hat) to reinforce meaning
- When it's **not a real word** (e.g., `dat`), nothing negative happens — it just sits quietly, no buzzer, no red X. Silence is the "not yet" signal, per Cognitive Load Theory guidance (no punishing feedback at this age).
- After finding all real words in one family, a "New Family" button loads the next ending (`-at` → `-an` → `-ig` → etc.)

**What we're testing:** Does the swipe-to-discover mechanic hold his attention for more than 2-3 minutes unprompted? Does he enjoy the "reveal" moment of a real word forming? This tells us whether to invest further.

---

## 2. Technical Specification

| Item | Spec |
|---|---|
| **Platform** | Web app, optimized for iPad Air 5 (Safari), touch-first |
| **Delivery** | Single HTML file — sent directly, no install/build step needed for the prototype |
| **Orientation** | Landscape preferred, but functional in portrait |
| **Input** | Touch swipe (vertical drag) on the letter slider |
| **Audio** | Web Speech API (`speechSynthesis`) for word pronunciation — no external audio files needed for prototype |
| **Visuals** | SVG/CSS-based illustrations for word families (simple, flat, high-contrast — no photorealistic images, per Cognitive Load Theory) |
| **Offline** | Fully self-contained, works with no internet connection once loaded |
| **Word data** | Hardcoded JSON array for prototype (~5 word families, 5-6 letters each = ~30 words) |
| **Session length target** | 3-5 minutes of play before natural stopping point ("New Family" exhausted) |
| **Accessibility** | Large touch targets (min 60x60px), no fine-motor precision required, high color contrast |
| **Out of scope for Phase 0** | Login, progress saving, adaptive difficulty, mic/speech-attempt tracking, reward currency — all deferred to Phase 1+ |

---

## 3. Technical Architecture

```
┌─────────────────────────────────────────┐
│           index.html (single file)        │
│                                             │
│  ┌───────────────────────────────────┐   │
│  │         App Component (state)       │   │
│  │  - currentFamily: "-at"              │   │
│  │  - currentLetter: "c"                 │   │
│  │  - foundWords: []                     │   │
│  └───────────────┬───────────────────┘   │
│                  │                          │
│      ┌───────────┼───────────┐             │
│      ▼           ▼           ▼             │
│  ┌────────┐ ┌──────────┐ ┌──────────┐    │
│  │ Slider │ │  Word     │ │  Reward   │    │
│  │ (input)│ │  Display  │ │  Popup    │    │
│  └────────┘ └──────────┘ └──────────┘    │
│                  │                          │
│                  ▼                          │
│           ┌──────────────┐                 │
│           │  Word Family  │                 │
│           │  Data (JSON)  │                 │
│           └──────────────┘                 │
│                  │                          │
│                  ▼                          │
│         ┌────────────────────┐             │
│         │ Web Speech API      │             │
│         │ (pronunciation)      │             │
│         └────────────────────┘             │
└─────────────────────────────────────────┘
```

**Data structure (word families):**
```json
{
  "families": [
    {
      "rime": "at",
      "letters": ["c","b","m","s","h","r","f"],
      "realWords": ["cat","bat","mat","sat","hat","rat"]
    },
    {
      "rime": "an",
      "letters": ["c","m","p","r","t","v"],
      "realWords": ["can","man","pan","ran","tan","van"]
    }
  ]
}
```

**State flow:**
1. Swipe gesture updates `currentLetter` (debounced so it doesn't fire on every pixel of drag)
2. `currentLetter + currentFamily.rime` is checked against `realWords`
3. If match → trigger celebration animation + speech synthesis + add to `foundWords`
4. If `foundWords.length === realWords.length` → show "New Family" button
5. No backend, no network calls — everything runs client-side in memory

---

## 4. How I'll Build It — Step by Step

**Step 1 — Static layout first (no logic)**
Build the visual shell: the slider track, the rime text, the word display area, with placeholder styling. Confirm it looks right and fits the iPad screen before adding any interactivity.

**Step 2 — Swipe mechanic**
Wire up touch/drag events on the slider to cycle through the letter array. Add subtle physics (momentum/snap-to-letter) so it feels tactile rather than jumpy — this is the single most important interaction to get right, since it's the whole game.

**Step 3 — Word detection + audio**
On every letter change, concatenate onset + rime, check against the real-word list, and trigger `speechSynthesis.speak()` when a match is found. Test pronunciation clarity on the actual iPad Safari (speech synthesis quality varies by browser).

**Step 4 — Celebration feedback**
Add the pop/bounce animation and simple SVG picture reveal for matched words. Keep this snappy (under 1 second) so it doesn't slow down repeated play.

**Step 5 — Family progression**
Add the "found all words" detection and the "New Family" transition, cycling through the 5 hardcoded families.

**Step 6 — Ship & test**
Send you the single HTML file. You open it directly in Safari on his iPad (or I can help you save it to his home screen as an icon). You watch him play, unprompted, for a few minutes.

**What I need from you after that:** just tell me what happened — did he swipe past the first "not yet" letters looking for real words, did he try to say the words back on his own, did he get bored after 1 minute or want more families. That's the signal that decides whether we move to Phase 1.

---

*Next step: reply "build it" and I'll generate the working prototype as an interactive artifact you can test with him directly.*
