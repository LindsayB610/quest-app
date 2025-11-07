PRD — Pocket Phonics Quest

Version: v1.3 (finalized)

Platforms: Chrome on macOS (deployed on Netlify)

Primary user: Lucas (6, first grade, ADHD)

Secondary user: Parent (setup, guidance, settings)

⸻

1) Product Summary

A kid‑sized, speech‑enabled reading game that blends systematic phonics practice with a light monster‑collecting adventure. The app speaks prompts (TTS), listens to Lucas reading words and saying dice numbers (STT), and dishes out stars, stickers, treasure, and habitat upgrades. Two short d6 checks per session add a feeling of control and surprise without overshadowing the reading.

	•	Session length: randomized 5–10 minutes (no picker); also aims for ~10 items per session; session ends when the first limit is reached (finish current item).

	•	Dice checks: 2 per session (sometimes 3 if the session runs >8 minutes).

	•	Theme: creature collecting, friendly "bad guys," treasure, and a home habitat (Camp, Treehouse, Pond).

	•	Tone: encouraging, concise, mid‑energy (neither saccharine nor stern).

	•	Comfort constraints: headphones preferred, limit flashing, no spiders.

⸻

2) Goals & Non‑Goals

Goals

	•	Short, repeatable practice that grows phonemic awareness and decoding from End‑K through End‑1st grade skills.

	•	Keep attention via predictable loops, tiny surprises, and immediate, specific feedback.

	•	Parent‑friendly visibility and full local privacy.

Non‑Goals (v1)

	•	No online accounts, cloud sync, or third‑party analytics.

	•	No multiplayer or social.

	•	No public shipping of copyrighted art.

⸻

3) Users & Context

	•	Lucas (6, ADHD): reads on a Mac laptop in Chrome, often with headphones.

	•	Parent: chooses content stage (or uses placement test), adjusts settings, optionally imports personal images via the Home Assets Mode.

⸻

4) Learning Scope & Sequence (10 Stages)

Each stage contains ~50–100 words internally; below are skill targets and example items.

	1.	Stage 1 — End‑K baseline

Letter‑sound review; short‑a CVC: cat, map, sad, bag, jam.

	2.	Stage 2 — Short e & i (CVC)

Contrast /e/ vs /i/: bed, web, pen, pig, fin, sit.

	3.	Stage 3 — Short o & u (CVC)

Contrast /o/ vs /u/: dog, hop, log, sun, cup, bug.

	4.	Stage 4 — Mixed CVC fluency

Switch among all short vowels; short phrases: jam, pen, lid, rod, cub, fox.

	5.	Stage 5 — Digraphs

sh, ch, th (voiced/unvoiced), wh: ship, chat, thin, this, when.

	6.	Stage 6 — Initial blends (s‑ & l‑)

st, sp, sk; bl, cl, fl, gl, pl, sl: stop, spin, clip, flag, plan.

	7.	Stage 7 — r‑blends & final blends

br, cr, dr, fr, gr, pr, tr; finals ‑nd, ‑mp, ‑st, ‑ft: frog, grab, drip, camp, hand, gift, last.

	8.	Stage 8 — Long vowels (CVCe)

a_e, i_e, o_e, u_e, e_e: cap→cape, kit→kite, hop→hope, cub→cube.

	9.	Stage 9 — Common vowel teams (lite)

ai/ay, ee/ea, oa/ow, igh: rain, play, feet, read, boat, snow, light.

	10.	Stage 10 — r‑controlled & wrap

ar, or, er/ir/ur, ‑s/‑ed/‑ing, easy 2‑syllables: car, corn, her, jumps, sunset.

Sight ("heart") words appear sparingly in Stages 4–10 (max 2 per session; not during frustration)—for example: the, said, was, you.

Mastery rule: A word is "mastered" after 3 correct reads across separate days (spaced). Mastered words drop to light review.

⸻

5) Quick Placement Test

When: First run; can be re‑run from Settings.

Length: 60–90 s, 8–10 items.

Flow: 2 letter‑sounds → 4 mixed‑vowel CVC → 2 digraph → 2 blend probes.

Placement rule:

	•	≥80% → start next stage

	•	50–79% → start tested stage

	•	<50% → start one stage earlier

AC‑PLACEMENT‑01: Always completes under 2 minutes; produces a clear one‑line stage recommendation.

⸻

6) ADHD‑Savvy Design

	•	One small task per screen; short TTS; big targets; minimal text.

	•	Immediate feedback with specific praise ("I heard short /a/ in the middle.").

	•	Low sensory load; Calm Mode available and can auto‑engage.

	•	Movement brain‑breaks (20–30 s) by default; breathing when Calm Mode is on.

⸻

7) Core Game Loop

	1.	Story beat (villain mischief / treasure / buddy to help).

	2.	Say the word: TTS prompt; Lucas speaks; STT scores exact / near / partial.

	3.	Feedback: one short, specific line + tiny progress.

	4.	d6 check (twice per session; maybe three on long sessions): "Roll your die. Say or type the number (1–6)."

	5.	Reward: stars → stickers → creature evolutions; treasure; habitat upgrades.

	6.	Brain break if needed, then continue until session end.

⸻

8) Character Creation (Kid‑Readable)

Steps (full‑screen; TTS at each step; 44px+ targets):

	1.	Buddy color → starter type

	2.	Hair (4–6)

	3.	Skin tone (6)

	4.	Outfit (4)

	5.	Name (large on‑screen A–Z; TTS spells letters; "Random" option; parent pronunciation override)

	6.	Mic check: "Say: hello!" → Finish

Dice shortcut: "Roll 1–6 to pick for me" at each step.

AC‑CHAR‑01: ≤6 choices per screen; TTS reads label on focus; Back always available.

⸻

9) d6 Mechanics (Simple by design)

	•	Frequency: 2 per session (auto‑adds a 3rd if session >8 min).

	•	Input: speech (one–six, robust to homophones like for/four, tree/three) or keyboard 1–6.

	•	DC (difficulty): 2–5 depending on moment.

• Success ≥ DC: bonus star/treasure or simpler next item.

• Fail: model target sound, partial progress, slightly easier next item.

	•	No advantage, no inspiration (keeps focus on reading).

AC‑DICE‑KEY‑01: Typing 1–6 shows "Rolled N" chip; Esc cancels; non‑1..6 ignored.

⸻

10) Speech & Scoring

TTS: speechSynthesis; neutral adult voice (en‑US), rate ~0.95, pitch neutral.

STT: webkitSpeechRecognition; single‑turn listening; start/stop beeps; 4–5 s timeout; one auto‑reprompt.

Word scoring (pragmatic v1):

	•	Exact: transcript equals target → full credit.

	•	Near: Levenshtein distance ≤1 (length‑aware) → partial‑plus.

	•	Partial: onset or rime match → small credit + specific feedback.

	•	Miss: model ("/k/ + /æt/ → cat"), move on with tiny progress.

Number parsing: whitelist {1..6, "one…six"} + fuzzy map (for→4, tree→3, sicks→6) with visible confirmation chip.

AC‑SPEECH‑01: Empty/short/noise is ignored; one reprompt; then model.

⸻

11) Adaptive Help & Frustration Detection

Signals: 3 no‑speech timeouts in 5 items; accuracy <50% over last 6 items; response latency >4 s (×3); rapid skips/backtracks.

Responses: slow TTS, show model, temporarily drop difficulty, start a short movement break, optionally prompt parent co‑read.

AC‑ADAPT‑01: Trigger cause + chosen response logged locally.

⸻

12) Calm Mode (Auto & Lockable)

What it changes: softer colors (reduced saturation), fewer particles, no flashes/shakes, slower TTS (~‑10% rate), lower SFX, simplified backgrounds, high contrast.

Auto‑engage on repeated frustration or if previous session ended "hot"; parent can lock On/Off.

AC‑CALM‑01: Toggling Calm Mode updates visuals/audio immediately without reload.

⸻

13) Visual Theme (Tokens & Usage)

Palette (WCAG‑aware) with primary uses:

	•	–brand-cerulean #2E6AE6 (primary action buttons, links)

	•	–brand-electric #FFD400 (capture/progress highlights, dark text on it)

	•	–brand-leaf #2FA36A (success, friendly "buddy")

	•	–brand-ember #FF7A21 (attention micro‑bursts)

	•	–brand-berry #7A5AF8 (secondary actions, badges)

	•	–ink-900 #0F172A (primary text)

	•	–cloud-100 #F1F5F9 (card surfaces)

	•	–cloud-50 #F8FAFC (app background)

	•	–danger #E5484D (errors)

	•	–success #22C55E (correct feedback)

Contrast guardrails

	•	Text on cerulean/berry/leaf → white.

	•	Text on electric → near‑black (ink‑900).

	•	All UI states meet ≥4.5:1 contrast.

Typography: Lexend; base 22–24 px; reading targets 32–40 px.

Components: pill buttons, card surfaces, capture meter in electric; sparkles max 600 ms (off in Calm Mode).

Calm Mode palette adjustments: reduce saturation ~25% and darken brand fills ~8%; limit motion.

⸻

14) Home Assets Mode (Copyright Posture)

	•	Default build ships only original placeholders.

	•	Parent‑gated importer lets the parent load images/audio from the local device (e.g., Pokémon creatures they already have) via File System Access or drag‑drop.

	•	Files are stored in IndexedDB, never uploaded, and not included in the Netlify build.

	•	Mapping UI assigns imported assets to slots (starter, evolution, encounter art, badges).

	•	On‑screen banner: "Imported files are for personal use on this device. Do not upload or share."

AC‑IP‑01: No imported files in build artifacts or network traffic; "Clear Assets" wipes IndexedDB.

Note: I won't find, link, or provide copyrighted assets. This mode exists so private files can be used safely at home.

⸻

15) Accessibility & Safety

	•	Minimum 44 px tap targets; keyboard navigable; focus outlines visible.

	•	Color contrast ≥ 4.5:1; no red‑green reliance.

	•	Low flicker; no spiders or frightening imagery.

⸻

16) Input Handling (Errant Keystrokes, Taps, Noise)

Modes: reading | dice | menu | textentry — only the active mode listens for input.

Key filters

	•	Reading: only Space (TTS pause/resume) and Esc (pause) do anything; all others ignored.

	•	Dice: accept 1–6 (top row or numpad); ignore repeats; show confirmation chip "Rolled N"; Esc cancels.

	•	Text entry (name): large on‑screen A–Z plus Backspace; other keys ignored.

Debounce: suppress auto‑repeat; 250 ms debounce on actionable keys.

Pointer: ignore stray drags/scroll; primary action buttons only.

STT noise guards: discard <200 ms utterances or null transcripts; reprompt once; then model.

Destructive actions: press‑and‑hold + 2‑step confirm (Reset, Clear Assets, Delete Save).

Auto‑save & recovery: save after every item; resume at last item on reload.

Acceptance Criteria

	•	AC‑KEY‑01: In reading mode, number keys have no effect; Space/Esc function as stated.

	•	AC‑KEY‑02: In dice mode, only 1–6 accepted; other keys ignored; Esc cancels.

	•	AC‑KEY‑03: Auto‑repeat yields a single event.

	•	AC‑RECOVER‑01: After refresh, resume at last safe checkpoint with settings intact.

⸻

17) Content Model (for lesson packs)

Stage pack (shape, illustrative):

{ "stageId": 1, "name": "End-K Baseline (Short A)", "targets": [ { "phoneme": "/æ/", "grapheme": "a" } ], "items": [ { "word": "cat", "onset": "c", "rime": "at" }, { "word": "map", "onset": "m", "rime": "ap" } ], "review": [], "diceMoments": [ { "dc": 2, "onSuccess": "bonus_star", "onFail": "model_then_easy" } ] }

Placement test (shape, illustrative):

{ "id": "placement-v1", "items": [ { "type": "letter-sound", "prompt": "Say the sound: m", "target": "/m/", "display": "m" }, { "type": "cvc", "word": "cat", "onset": "c", "rime": "at" }, { "type": "digraph", "word": "ship", "pattern": "sh" }, { "type": "blend", "word": "flag", "pattern": "fl" } ], "rules": { "advanceAt": 0.8, "stayAt": 0.5 } }

⸻

18) Technical Overview

	•	Frontend: React (or similar) with small state store; Vite‑built SPA.

	•	Speech: Web Speech API — speechSynthesis (TTS), webkitSpeechRecognition (STT); turn‑taking UI with start/stop beeps.

	•	Persistence: localStorage (progress/settings), IndexedDB (imported assets).

	•	PWA: offline cache for app + lesson packs; STT may require connectivity.

	•	Hosting: Netlify (HTTPS, SPA redirects).

	•	Privacy: no accounts, ads, or third‑party analytics; Export/Import JSON for backup.

⸻

19) Rewards & Economy

	•	Stars (primary progression), Stickers (cosmetic), Badges (milestones), Treasure (habitat upgrades), Creature evolutions (visual).

	•	Streaks: gentle—bonus stars for consecutive days; never reset progress to zero.

⸻

20) Acceptance Criteria (Highlights)

	•	AC‑PLACEMENT‑01: Placement test under 2 minutes; clear stage output.

	•	AC‑SPEECH‑01: Exact/near/partial scoring with distinct feedback lines.

	•	AC‑DICE‑SAY‑01: Speech parses "four/for/4" → 4 with confirmation; parent can edit.

	•	AC‑DICE‑KEY‑01: Keyboard 1–6 accepted; mis‑keys ignored; Esc cancels.

	•	AC‑CALM‑01: Calm Mode changes visuals/audio immediately.

	•	AC‑IP‑01: Imported assets never appear in build or network requests.

	•	AC‑PWA‑01: Installs; runs offline; lesson packs cached.

⸻

21) QA Test Plan (Abridged)

	•	Mic permissions: allow/deny/revoke mid‑session; clear recovery.

	•	Noisy rooms: STT timeouts, reprompts, and model flows.

	•	Dice edge cases: "for/four," "tree/three," "sicks/six"; keyboard 7/0 ignored; Esc cancel.

	•	Calm Mode: auto‑trigger on frustration and manual toggle.

	•	Session timer: random selection between 5–10 min; clean end (finish item).

	•	Home Assets: import, map, clear; verify no network egress.

	•	Accessibility: tab order, focus outline, color contrast, target sizes.

⸻

22) Local Metrics (Parent‑Visible)

	•	Sessions/week; average minutes/session; completion rate.

	•	Accuracy by stage and skill; first‑try rate; average response latency.

	•	Number‑word accuracy (dice 1–6).

	•	Frustration events (count and cause).

(All metrics are local only; no external analytics.)

⸻

23) Parents' Guide (App Copy)

What is this?

A 5–10 minute practice game that helps build early decoding through short, focused reps. Lucas hears a word, says it back, and earns fun rewards. Small dice moments add control and variety.

Why these levels?

We follow a well‑established phonics sequence for K–1: CVC short vowels → digraphs → blends → silent‑e (long vowels) → common vowel teams → r‑controlled vowels, with a few heart words later on. This mirrors typical first‑grade expectations and keeps the path predictable.

How the placement check works

A 60–90 second probe (letters → CVC → digraphs/blends) sets a sensible starting stage. It's quick so we spend time practicing, not testing.

What "counts"

	•	Exact match = full credit.

	•	Near match = small difference (e.g., kat for cat).

	•	Partial = right start sound or ending sound.

The game always models the correct word and moves forward so momentum stays high.

Dice checks

Two quick d6 moments per session; Lucas can say or type 1–6. Success tweaks rewards/branching; failure gives a quick model and a simpler next step. No complicated rules.

If he gets frustrated

The app slows down, models more, offers a short movement break, and can switch into Calm Mode automatically. You'll see gentle prompts for co‑reading when helpful.

Privacy

No accounts, no ads, no cloud. Progress stays on the device. You can export/import a local JSON backup. If you enable Home Assets Mode, any images you import stay local on this device.

⸻

24) Microcopy (Kid‑Level)

	•	Prompt: "Say: cat."

	•	Near‑match feedback: "Close! I heard the start sound /k/."

	•	Model: "Try with me: /k/ … /æt/ … cat."

	•	Dice: "Roll your die. Say or type the number." → "Rolled five!"

	•	Break: "Stretch break! Reach up high—three breaths."

⸻

25) Two‑Week Quest Plan (Starter)

Session template: ~10 items; 2 dice checks (3rd if long). Rewards: stars, stickers, treasure, habitat tiles, creature evolution.

Week 1

	•	Day 1: Stage 1 (cat, mat, map, sad, tap, bag). Dice DC 2. Reward: Hatch + sticker.

	•	Day 2: Stage 1 (jam, pan, rag, cap, ham, wax). Dice DC 2. Reward: Tent.

	•	Day 3: Stage 2 intro (bed, men, pen, web, pig, fin). Dice DC 3. Reward: Level 2.

	•	Day 4: Stage 2 (sit, win, lid, zip, jet, rid). Dice DC 3. Reward: sticker.

	•	Day 5: Stage 3 intro (dog, log, mop, hop, sun, bug). Dice DC 3. Reward: Campfire.

	•	Day 6: Stage 3 (cup, rug, tub, cob, fox, pod). Dice DC 3. Reward: Wood Planks.

	•	Day 7: Mini‑Boss (mixed CVC). Dice DC 4, DC 3. Reward: CVC Scout badge + area unlock.

Week 2

	•	Day 8: Stage 5 sh/ch (ship, shop, fish, chat, much, rich). Dice DC 3.

	•	Day 9: Stage 5 th/wh (thin, this, math, with, when, whip). Dice DC 3.

	•	Day 10: Stage 6 s‑blends (stop, spin, skip, spot, slip, step). Dice DC 4.

	•	Day 11: Stage 6 l‑blends (flag, clap, plan, plug, clip, plum). Dice DC 4.

	•	Day 12: Stage 7 r‑blends (frog, grab, drip, trip, grin, pram). Dice DC 4.

	•	Day 13: Stage 7 finals (hand, gift, last, lamp, nest, best). Dice DC 4. Reward: Blend Beginner badge.

	•	Day 14: Boss (mixed to Stage 7). Dice DC 4, DC 5. Reward: Evolution + habitat upgrade.

⸻

26) Roadmap (Build Order)

	1.	Skeleton app + local persistence + session timer randomizer.

	2.	TTS/STT wrappers and reading item loop with scoring.

	3.	Dice input (speech + keyboard 1–6), DC outcomes.

	4.	Character Creator (TTS, per‑step choices, name entry).

	5.	Adaptive/frustration + Calm Mode auto.

	6.	Rewards layer (stars, stickers, badges, habitat tiles).

	7.	Placement test + stage packs loader.

	8.	Home Assets Mode (import/map/clear) with parental gate.

	9.	PWA/offline + export/import JSON.

	10.	Parent dashboard (local metrics).

