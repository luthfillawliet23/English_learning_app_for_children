# Project Handoff: English Learning App for Son (age 6)

## Context for Claude Code
This project started in a Claude chat conversation and is now moving to Claude Code for Phase 2+. This document summarizes everything built so far and the reasoning behind key decisions, so work can continue without re-deriving context.

---

## Child Profile
- **Age:** 6
- **Language:** English is his dominant/first spoken language (he has a speech delay and began talking in English rather than Bahasa Indonesia, the home language)
- **Reading level:** Already reading simple sentences — ahead of typical phonics-basics stage
- **Learning tool:** iPad Air 5, currently also used for Kumon worksheets
- **Design implication:** Since he's verbally delayed but literate, the app should pair his reading strength with **low-pressure speech practice** — track *attempt made*, never judge pronunciation accuracy (that's a speech pathologist's role, not the app's).

## Source Framework
`Educational_Psychology_App_Framework.docx` is in the repo — written by an educational psychologist, grounded in Vygotsky's Zone of Proximal Development, Cognitive Load Theory, and Skinner's reinforcement scheduling. Key principles already implemented:
- **No negative feedback ever** — wrong/incomplete attempts are met with silence, not a buzzer or red X ("not yet," not "wrong")
- **Variable reward schedule** — bonus celebrations at streak intervals, not every correct answer
- **Multimodal reinforcement** — picture + sound + text together on every win

## Repo
`github.com/luthfillawliet23/English_learning_app_for_children` (public, master branch, deployed via GitHub Pages)

## Progress So Far

**Phase 0 — Word Family Slider (validated with real testing):**
Single-game prototype. Swipe a letter up/down against a fixed word ending to discover real words (cat, bat, hat...). Tested directly with him — engagement was under 2 minutes on the one session logged, but the **picture + sound reveal moment was clearly what grabbed him most**, more than the swipe/hunt mechanic itself. This shaped Phase 1's design: lean into fast, frequent payoff moments rather than long exploration.

**Phase 1 — Learning Hub (built, not yet tested with him):**
`learning_hub.html` — single-file web app (no build step, no framework, vanilla JS/HTML/CSS), hosted via GitHub Pages, works offline once loaded.
- Home screen with two game tiles and a shared star count
- Word Family Slider (ported from Phase 0, identical mechanics)
- Sight Word Memory Match (new — flip cards, hear word, find pairs)
- Shared reward engine: stars + streak counter + bonus celebration every 5 correct in a row
- `localStorage` persistence: total stars, per-word mastery counts, and a capped interaction log (`{game, word, correct, timestamp}`) for future analysis
- **Important fix already made:** rewards only fire on genuine interaction — an earlier version accidentally granted a free star just from reloading the page; this is now prevented

## Known Gotchas (already solved once — don't rediscover)
- **GitHub Pages** needs to be manually enabled (Settings → Pages → Deploy from branch → master → root) and can take a few minutes to go live after first setup or a push
- **iOS Safari button taps** can be unreliable with a plain `click` listener — use a dual `touchend` + `click` handler with a debounce guard (already implemented as `addTapListener()` in the codebase)
- **Don't open the HTML file locally via the Files app** — it can load through Quick Look instead of real Safari and misbehave. Always test via the live GitHub Pages URL.

## Planned Next Phases
- **Phase 2:** "Read It, Say It" (short sentences, tap-to-hear words, mic-based speech *attempt* tracking — not accuracy scoring) + Guided Trace (finger-tracing letters/words)
- **Phase 3:** Story Comprehension matching game + a simple parent dashboard surfacing the logged interaction data (which words/sounds he struggles with, session length trends, etc.)

## Design Principles to Keep Consistent
1. Session target: 3-5 minutes, repeatable — not long single sessions
2. Every reward = picture + sound + text together
3. No punishing feedback, ever
4. Rewards follow real interaction only, never passive/automatic
5. Speech features track effort/attempt, never correctness
