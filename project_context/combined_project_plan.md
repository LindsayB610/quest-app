# Combined Project Plan: Pocket Phonics Quest

## Overview

This plan combines the PRD requirements with Pokémon graphics integration considerations to build a speech-enabled phonics reading game for quest.lindsaybrunner.com.

**Key Integration Points:**
- Use community Pokémon components: `react-pokemon` npm package, PkIcon JS React component
- PokéAPI for fetching Pokémon data and image URLs
- Home Assets Mode (Phase 8) will allow parents to import additional personal Pokémon images locally
- Creature collecting theme aligns with monster-collecting mechanics

---

## Phase 1: Foundation & Infrastructure
**Goal:** Establish core app structure, routing, and persistence

### Tasks:
- [ ] Set up React + Vite project structure
- [ ] Install Pokémon-related dependencies
  - `react-pokemon` npm package
  - Consider PkIcon JS React component patterns
- [ ] Configure Netlify deployment (netlify.toml with SPA redirects)
- [ ] Implement routing structure (React Router)
- [ ] Set up state management (Context API or Zustand)
- [ ] Create localStorage wrapper for progress/settings persistence
- [ ] Set up IndexedDB wrapper for imported assets (Home Assets Mode prep)
- [ ] Implement session timer randomizer (5-10 minutes)
- [ ] Create basic app shell with navigation
- [ ] Set up design system foundation (CSS variables for color tokens)
- [ ] Implement Lexend font loading
- [ ] Create base component library (buttons, cards, etc.)
- [ ] Set up PokéAPI integration utilities (for fetching Pokémon data)

### Deliverables:
- Working SPA skeleton
- Local persistence working
- Session timer logic
- Design tokens established

### Acceptance Criteria:
- App loads on quest.lindsaybrunner.com
- Settings persist across sessions
- Timer randomizes correctly between 5-10 minutes

---

## Phase 2: Speech Infrastructure
**Goal:** Build TTS/STT wrappers and core reading loop

### Tasks:
- [ ] Create TTS wrapper (speechSynthesis API)
  - Neutral adult voice (en-US)
  - Rate ~0.95, pitch neutral
  - Pause/resume with Space key
- [ ] Create STT wrapper (webkitSpeechRecognition)
  - Single-turn listening
  - Start/stop beeps
  - 4-5s timeout
  - One auto-reprompt
  - Noise guards (<200ms utterances, null transcripts)
- [ ] Implement word scoring algorithm
  - Exact match (transcript === target)
  - Near match (Levenshtein distance ≤1, length-aware)
  - Partial match (onset or rime match)
  - Miss handling with modeling
- [ ] Create reading item loop component
  - TTS prompt → STT listen → Score → Feedback
  - Progress tracking per item
- [ ] Implement feedback system
  - Specific praise messages
  - Progress indicators
  - Model prompts ("/k/ + /æt/ → cat")
- [ ] Add mic permission handling
  - Request flow
  - Deny/revoke recovery
  - Clear error messages

### Deliverables:
- Working TTS/STT integration
- Reading loop with scoring
- Feedback system

### Acceptance Criteria:
- AC-SPEECH-01: Exact/near/partial scoring with distinct feedback
- TTS speaks clearly at correct rate
- STT handles timeouts and reprompts gracefully
- Empty/noise inputs are ignored

---

## Phase 3: Dice Mechanics
**Goal:** Implement d6 checks with speech and keyboard input

### Tasks:
- [ ] Create dice check component
- [ ] Implement dice input modes
  - Speech recognition (1-6, "one" through "six")
  - Keyboard input (1-6, top row or numpad)
  - Fuzzy number parsing (for→4, tree→3, sicks→6)
- [ ] Create confirmation chip UI ("Rolled N")
- [ ] Implement DC (difficulty) system (2-5)
- [ ] Create success/failure outcomes
  - Success ≥ DC: bonus star/treasure or easier next item
  - Fail: model target sound, partial progress, easier next item
- [ ] Add dice scheduling logic
  - 2 per session (auto-add 3rd if session >8 min)
- [ ] Implement input mode filtering
  - Only dice mode accepts 1-6
  - Esc cancels dice input
  - Other keys ignored in dice mode
- [ ] Add debouncing (250ms) for keyboard input

### Deliverables:
- Working dice checks
- Speech and keyboard input
- DC outcomes

### Acceptance Criteria:
- AC-DICE-KEY-01: Keyboard 1-6 accepted; mis-keys ignored; Esc cancels
- AC-DICE-SAY-01: Speech parses "four/for/4" → 4 with confirmation
- Dice checks trigger at correct intervals
- Success/failure outcomes work correctly

---

## Phase 4: Character Creation
**Goal:** Build kid-friendly character creator with TTS support

### Tasks:
- [ ] Create character creator flow
  - Step 1: Buddy color → starter type
  - Step 2: Hair selection (4-6 options)
  - Step 3: Skin tone (6 options)
  - Step 4: Outfit (4 options)
  - Step 5: Name entry (large on-screen A-Z keyboard)
  - Step 6: Mic check ("Say: hello!")
- [ ] Implement TTS for each step
  - Reads labels on focus
  - Spells letters during name entry
- [ ] Create on-screen keyboard component
  - Large A-Z buttons (44px+ targets)
  - Backspace support
  - "Random" name option
  - Parent pronunciation override
- [ ] Add dice shortcut ("Roll 1-6 to pick for me")
- [ ] Implement navigation (Back button always available)
- [ ] Create character data model
- [ ] Save character to localStorage

### Deliverables:
- Complete character creator
- TTS support throughout
- Character persistence

### Acceptance Criteria:
- AC-CHAR-01: ≤6 choices per screen; TTS reads label on focus; Back always available
- All steps complete successfully
- Character saves and loads correctly

---

## Phase 5: Adaptive Systems & Calm Mode
**Goal:** Implement frustration detection and Calm Mode

### Tasks:
- [ ] Create frustration detection system
  - Signals: 3 no-speech timeouts in 5 items
  - Accuracy <50% over last 6 items
  - Response latency >4s (×3)
  - Rapid skips/backtracks
- [ ] Implement adaptive responses
  - Slow TTS
  - Show model
  - Temporarily drop difficulty
  - Start movement break
  - Optional parent co-read prompt
- [ ] Create Calm Mode system
  - Visual changes: softer colors (reduce saturation ~25%), darken fills ~8%
  - Fewer particles, no flashes/shakes
  - Simplified backgrounds, high contrast
  - Audio changes: slower TTS (~-10% rate), lower SFX
- [ ] Implement Calm Mode auto-engage
  - Trigger on repeated frustration
  - Trigger if previous session ended "hot"
- [ ] Add parent lock (On/Off toggle)
- [ ] Create movement break component (20-30s)
- [ ] Add breathing exercises (when Calm Mode on)
- [ ] Implement local logging (trigger cause + response)

### Deliverables:
- Frustration detection working
- Calm Mode with auto-engage
- Adaptive help system

### Acceptance Criteria:
- AC-ADAPT-01: Trigger cause + chosen response logged locally
- AC-CALM-01: Toggling Calm Mode updates visuals/audio immediately
- Frustration signals detected correctly
- Adaptive responses trigger appropriately

---

## Phase 6: Rewards & Economy
**Goal:** Build rewards system and progression

### Tasks:
- [ ] Create rewards data model
  - Stars (primary progression)
  - Stickers (cosmetic)
  - Badges (milestones)
  - Treasure (habitat upgrades)
  - Creature evolutions (visual)
- [ ] Implement star system
  - Award on correct reads
  - Track totals
  - Display in UI
- [ ] Create sticker system
  - Unlock mechanics
  - Display collection
- [ ] Build badge system
  - Milestone tracking
  - Badge unlock logic
  - Display earned badges
- [ ] Create treasure/habitat system
  - Habitat types (Camp, Treehouse, Pond)
  - Upgrade mechanics
  - Visual representation
- [ ] Implement creature evolution system
  - Evolution triggers
  - Visual evolution display
  - Use react-pokemon component for creature displays
  - Fetch evolution data from PokéAPI
- [ ] Add streak system
  - Track consecutive days
  - Bonus stars for streaks
  - Never reset progress to zero
- [ ] Create rewards UI components
  - Star display
  - Sticker collection view
  - Badge showcase
  - Habitat view
  - Creature display (using react-pokemon and PokéAPI sprites)

### Deliverables:
- Complete rewards system
- Progression tracking
- Visual reward displays

### Acceptance Criteria:
- Stars awarded correctly
- Badges unlock at milestones
- Streaks tracked accurately
- Rewards persist across sessions

---

## Phase 7: Content System & Placement Test
**Goal:** Build stage packs, placement test, and content loading

### Tasks:
- [ ] Create content data models
  - Stage pack structure (10 stages)
  - Placement test structure
  - Word items with onset/rime
  - Dice moment definitions
- [ ] Build stage pack loader
  - Load from JSON files
  - Cache in localStorage/IndexedDB
- [ ] Create placement test component
  - 8-10 items (2 letter-sounds → 4 CVC → 2 digraph → 2 blend)
  - 60-90 second flow
  - Scoring logic
- [ ] Implement placement rules
  - ≥80% → start next stage
  - 50-79% → start tested stage
  - <50% → start one stage earlier
- [ ] Create stage content for all 10 stages
  - Stage 1: End-K baseline (short-a CVC)
  - Stage 2: Short e & i (CVC)
  - Stage 3: Short o & u (CVC)
  - Stage 4: Mixed CVC fluency
  - Stage 5: Digraphs
  - Stage 6: Initial blends (s- & l-)
  - Stage 7: r-blends & final blends
  - Stage 8: Long vowels (CVCe)
  - Stage 9: Common vowel teams
  - Stage 10: r-controlled & wrap
- [ ] Implement mastery tracking
  - 3 correct reads across separate days = mastered
  - Mastered words drop to light review
- [ ] Add sight word handling (max 2 per session, not during frustration)

### Deliverables:
- All 10 stage packs
- Working placement test
- Content loading system

### Acceptance Criteria:
- AC-PLACEMENT-01: Placement test under 2 minutes; clear stage output
- All stages load correctly
- Mastery tracking works
- Content persists and loads efficiently

---

## Phase 8: Home Assets Mode
**Goal:** Implement parent-gated asset import system and integrate Pokémon components

### Tasks:
- [ ] Integrate `react-pokemon` component
  - Install and configure
  - Use for displaying default Pokémon creatures
- [ ] Set up PokéAPI integration
  - Fetch Pokémon data and sprite URLs
  - Cache responses locally
  - Handle API errors gracefully
- [ ] Create Home Assets Mode UI
  - Parent gate (password/pin or explicit parent mode)
  - Import interface
  - Asset mapping UI
- [ ] Implement file import
  - File System Access API support
  - Drag-and-drop support
  - Image file validation
  - Audio file validation (optional)
- [ ] Create asset mapping system
  - Assign to slots: starter, evolution, encounter art, badges
  - Preview imported assets
  - Clear/remove assets
  - Override default PokéAPI assets with imported ones
- [ ] Implement IndexedDB storage
  - Store imported files
  - Never include in build
  - Never send over network
- [ ] Add asset loading system
  - Load from IndexedDB (imported assets)
  - Fallback to PokéAPI (default assets)
  - Display in game using react-pokemon or custom components
- [ ] Create warning banner
  - "Imported files are for personal use on this device. Do not upload or share."
- [ ] Implement "Clear Assets" functionality
  - Wipes IndexedDB
  - Resets to PokéAPI defaults
- [ ] Add asset validation
  - File type checks
  - Size limits
  - Error handling

### Deliverables:
- Working asset import system
- Asset mapping UI
- Secure local storage

### Acceptance Criteria:
- AC-IP-01: No imported files in build artifacts or network traffic
- Assets import and display correctly
- Clear Assets wipes IndexedDB completely
- Parent gate works correctly

---

## Phase 9: PWA & Data Management
**Goal:** Make app installable, offline-capable, and add data export/import

### Tasks:
- [ ] Create PWA manifest
  - App name, icons, theme colors
  - Display mode
  - Start URL
- [ ] Implement service worker
  - Cache app shell
  - Cache lesson packs
  - Offline fallback
  - Update strategy
- [ ] Add install prompt
  - "Add to Home Screen" flow
  - Install detection
- [ ] Create export/import system
  - Export to JSON (progress, settings, character)
  - Import from JSON
  - Validation
  - Error handling
- [ ] Implement offline detection
  - Show offline indicator
  - Handle STT connectivity requirement gracefully
- [ ] Add update notifications
  - Check for service worker updates
  - Prompt to refresh

### Deliverables:
- Installable PWA
- Offline functionality
- Export/import working

### Acceptance Criteria:
- AC-PWA-01: Installs; runs offline; lesson packs cached
- Export creates valid JSON
- Import restores state correctly
- Service worker updates work

---

## Phase 10: Parent Dashboard & Polish
**Goal:** Build parent metrics dashboard and final polish

### Tasks:
- [ ] Create parent dashboard component
  - Sessions/week display
  - Average minutes/session
  - Completion rate
  - Accuracy by stage and skill
  - First-try rate
  - Average response latency
  - Number-word accuracy (dice 1-6)
  - Frustration events (count and cause)
- [ ] Implement metrics collection
  - Track all required metrics
  - Store locally only
  - No external analytics
- [ ] Create settings page
  - Stage selection
  - Calm Mode toggle
  - Placement test re-run
  - Export/import controls
  - Home Assets Mode access
  - Reset options (with confirmation)
- [ ] Add Parents' Guide content
  - In-app help section
  - FAQ-style content
- [ ] Implement input mode filtering
  - Reading mode: only Space/Esc work
  - Dice mode: only 1-6 accepted
  - Text entry: only A-Z + Backspace
  - Debouncing on all inputs
- [ ] Add auto-save & recovery
  - Save after every item
  - Resume at last checkpoint on reload
- [ ] Implement destructive action confirmations
  - Press-and-hold + 2-step confirm
  - Reset, Clear Assets, Delete Save
- [ ] Final accessibility audit
  - 44px+ tap targets
  - Keyboard navigation
  - Focus outlines
  - Color contrast (≥4.5:1)
  - Screen reader testing
- [ ] Performance optimization
  - Bundle size optimization
  - Lazy loading
  - Image optimization
- [ ] Cross-browser testing (Chrome on macOS primary)

### Deliverables:
- Complete parent dashboard
- All settings functional
- Polished, accessible app

### Acceptance Criteria:
- AC-KEY-01: In reading mode, number keys have no effect; Space/Esc function as stated
- AC-KEY-02: In dice mode, only 1-6 accepted; other keys ignored; Esc cancels
- AC-KEY-03: Auto-repeat yields a single event
- AC-RECOVER-01: After refresh, resume at last safe checkpoint with settings intact
- All metrics display correctly
- Accessibility standards met

---

## Phase 11: Testing & QA
**Goal:** Comprehensive testing against acceptance criteria

### Tasks:
- [ ] Mic permissions testing
  - Allow/deny/revoke mid-session
  - Clear recovery flows
- [ ] STT testing
  - Noisy room scenarios
  - Timeouts and reprompts
  - Model flows
- [ ] Dice edge case testing
  - "for/four", "tree/three", "sicks/six"
  - Keyboard 7/0 ignored
  - Esc cancel works
- [ ] Calm Mode testing
  - Auto-trigger on frustration
  - Manual toggle
  - Visual/audio changes immediate
- [ ] Session timer testing
  - Random 5-10 min selection
  - Clean end (finish current item)
- [ ] Home Assets testing
  - Import, map, clear
  - Verify no network egress
  - IndexedDB isolation
- [ ] Accessibility testing
  - Tab order
  - Focus outlines
  - Color contrast
  - Target sizes
  - Screen reader compatibility
- [ ] Cross-device testing
  - Chrome on macOS (primary)
  - Other browsers (fallback)
- [ ] Performance testing
  - Load times
  - Memory usage
  - Smooth animations

### Deliverables:
- Test results documentation
- Bug fixes
- Performance report

### Acceptance Criteria:
- All AC-* criteria met
- No critical bugs
- Performance acceptable

---

## Phase 12: Deployment & Documentation
**Goal:** Deploy to production and create documentation

### Tasks:
- [ ] Final Netlify configuration
  - Subdomain setup (quest.lindsaybrunner.com)
  - DNS configuration
  - SSL certificate
  - Build optimization
- [ ] Create deployment documentation
  - Setup instructions
  - Environment variables (if any)
  - Build process
- [ ] Write user documentation
  - Quick start guide
  - Parent guide (in-app + external)
  - Troubleshooting
- [ ] Create developer documentation
  - Code structure
  - Component documentation
  - API references (internal)
- [ ] Set up monitoring (local only, no external analytics)
  - Error logging (local)
  - Performance tracking (local)
- [ ] Final production checks
  - HTTPS working
  - All features functional
  - No console errors
  - Privacy compliance verified

### Deliverables:
- Production deployment
- Complete documentation
- Monitoring setup

### Acceptance Criteria:
- App accessible at quest.lindsaybrunner.com
- All documentation complete
- Privacy requirements met
- No external analytics or tracking

---

## Technical Stack Summary

- **Frontend:** React + Vite
- **State Management:** Context API or Zustand
- **Routing:** React Router
- **Speech:** Web Speech API (speechSynthesis, webkitSpeechRecognition)
- **Storage:** localStorage (progress/settings), IndexedDB (imported assets)
- **PWA:** Service Worker, Web App Manifest
- **Pokémon Components:** `react-pokemon` npm package, PokéAPI for data/sprites
- **Hosting:** Netlify
- **Styling:** CSS with custom properties (design tokens)
- **Typography:** Lexend font
- **Build:** Vite
- **Deployment:** Netlify (SPA redirects)

---

## Key Design Principles

1. **ADHD-Savvy:** One task per screen, immediate feedback, low sensory load
2. **Privacy First:** No accounts, no cloud, no third-party analytics
3. **Accessibility:** WCAG compliant, keyboard navigable, screen reader friendly
4. **Local First:** All data stored locally, export/import for backup
5. **Community Components:** Use established Pokémon community components (react-pokemon, PokéAPI) for default creature assets

---

## Notes on Pokémon Integration

Based on the Pokémon graphics integration guide, this project will:
- **USE** `react-pokemon` npm package for displaying Pokémon components
- **USE** PokéAPI for fetching Pokémon data and official artwork/sprite URLs
- **USE** community patterns (PkIcon JS React component) as reference
- **DO** provide Home Assets Mode for parents to import additional personal files
- Follow standard React practices for rendering Pokémon images from API URLs
- Default creature assets come from PokéAPI; imported assets override defaults when provided

The creature collecting theme aligns with the monster-collecting mechanics, using PokéAPI and community components for the default experience.

