# Pocket Phonics Quest — Detailed Project Plan

**Version:** v1.0  
**Platforms:** Chrome on macOS (deployed on Netlify at quest.lindsaybrunner.com)  
**Primary User:** Lucas (6, first grade, ADHD)  
**Secondary User:** Parent (setup, guidance, settings)

---

## 📋 Table of Contents

* Overview _expand\_more_
  * Executive Summary
  * Understanding React + TypeScript
  * Current Project Analysis
* Strategy & Planning _expand\_more_
  * Technology Stack Decisions
  * Material UI Theming Strategy
  * Library Choices & Rationale
  * File Structure
  * State Management Architecture
  * Effort Estimation
* Implementation _expand\_more_
  * Implementation Details
  * Speech API Integration
  * Pokémon Integration Strategy
  * Content Data Models
  * Persistence Strategy
  * Accessibility Implementation
  * Performance Considerations
* Build Phases _expand\_more_
  * Phase 1: Foundation & Infrastructure
  * Phase 2: Speech Infrastructure
  * Phase 3: Dice Mechanics
  * Phase 4: Character Creation
  * Phase 5: Adaptive Systems & Calm Mode
  * Phase 6: Rewards & Economy
  * Phase 7: Content System & Placement Test
  * Phase 8: Home Assets Mode
  * Phase 9: PWA & Data Management
  * Phase 10: Parent Dashboard & Polish
  * Phase 11: Testing & QA
  * Phase 12: Deployment & Documentation
* Project Management _expand\_more_
  * Risk Factors
  * Decision Points
  * Success Criteria
  * Testing Strategy
  * Performance Budgets
* Reference _expand\_more_
  * Resources
  * Technical Details
  * Changelog
  * Next Steps

---

## 💡 Executive Summary

This document outlines the comprehensive plan and effort estimation for building **Pocket Phonics Quest**, a speech-enabled phonics reading game that blends systematic phonics practice with a light monster-collecting adventure.

**Key Finding**: Using Material UI as the component library and established Pokémon community components (react-pokemon, PokéAPI) can reduce development effort by **30-40%** compared to building all UI components from scratch.

**Technology Stack**: React 18 + TypeScript 5 + Vite (frontend), Material UI (components), Zustand (state), Web Speech API (TTS/STT), PokéAPI (creature data), Netlify (hosting)

**Primary Goals**:
- Short, repeatable practice (5-10 min sessions) for End-K through End-1st grade phonics skills
- ADHD-savvy design with immediate feedback and low sensory load
- Full local privacy (no accounts, no cloud, no third-party analytics)
- Parent-friendly visibility and control

**Estimated Total Effort**: 180-240 hours across 12 phases

---

## Understanding React + TypeScript

### They Work Together, Not Separately

**Important**: TypeScript and React are not alternatives—they work together. Everything in this project will be built using **React components written in TypeScript**.

* **TypeScript** = The programming language (typed JavaScript)
* **React** = The UI framework/library for building components
* **Material UI** = Component library built on React
* **Result** = React components written in TypeScript (`.tsx` files) using Material UI components

### What Gets Built as React Components (TypeScript)

All UI components will be React components written in TypeScript:

* **Pages**: `WelcomePage.tsx`, `CharacterCreatorPage.tsx`, `GamePage.tsx`, `PlacementTestPage.tsx`, `ParentDashboardPage.tsx`, `SettingsPage.tsx`
* **Game Components**: `ReadingItem.tsx`, `DiceCheck.tsx`, `FeedbackDisplay.tsx`, `ProgressIndicator.tsx`, `RewardAnimation.tsx`
* **Character Components**: `CharacterCreator.tsx`, `OnScreenKeyboard.tsx`, `BuddyDisplay.tsx`, `HabitatView.tsx`
* **UI Components**: `Modal.tsx`, `Button.tsx`, `Card.tsx`, `Chip.tsx` (wrapping Material UI)
* **Shared Components**: `Layout.tsx`, `Navigation.tsx`, `AccessibilityAnnouncer.tsx`

### What Gets Built as React Hooks (TypeScript)

Business logic will be React hooks written in TypeScript:

* **`useTTS.ts`** - Text-to-speech wrapper with TypeScript types
* **`useSTT.ts`** - Speech-to-text wrapper with TypeScript types
* **`useWordScoring.ts`** - Word scoring algorithm with TypeScript types
* **`useDiceCheck.ts`** - Dice mechanics logic
* **`useSessionTimer.ts`** - Session timer and item counting
* **`useFrustrationDetection.ts`** - Adaptive help system
* **`useCalmMode.ts`** - Calm Mode state and effects
* **`usePokemonAPI.ts`** - PokéAPI integration hook
* **`useHomeAssets.ts`** - Home Assets Mode file management
* **`usePersistence.ts`** - localStorage/IndexedDB wrappers

### What Gets Built as TypeScript Utilities

Pure functions and utilities written in TypeScript (no React):

* **`wordScoring.ts`** - Word scoring algorithms (Levenshtein, onset/rime matching)
* **`numberParsing.ts`** - Dice number parsing (speech to number)
* **`sessionTimer.ts`** - Session timer logic
* **`masteryTracking.ts`** - Word mastery calculation
* **`placementTest.ts`** - Placement test scoring and recommendations
* **`api/pokeapi.ts`** - PokéAPI client with TypeScript types
* **`storage/localStorage.ts`** - localStorage wrapper with types
* **`storage/indexedDB.ts`** - IndexedDB wrapper with types

### What Gets Built as TypeScript State Management

State management using Zustand with TypeScript:

* **`store/gameStore.ts`** - Game state (current item, progress, session)
* **`store/characterStore.ts`** - Character data
* **`store/rewardsStore.ts`** - Stars, stickers, badges, creatures
* **`store/settingsStore.ts`** - Settings, Calm Mode, stage selection
* **`store/types.ts`** - TypeScript type definitions for all state structures

### Example: React Component in TypeScript with Material UI

```typescript
// File: src/components/game/ReadingItem.tsx
// This is a React component written in TypeScript using Material UI

import React, { useState, useEffect } from 'react';
import { Box, Typography, Button, CircularProgress } from '@mui/material';
import { useTTS } from '../../hooks/useTTS';
import { useSTT } from '../../hooks/useSTT';
import { useWordScoring } from '../../hooks/useWordScoring';
import { useGameStore } from '../../store/gameStore';

interface ReadingItemProps {
  word: string;
  onset: string;
  rime: string;
  onComplete: (score: 'exact' | 'near' | 'partial' | 'miss') => void;
}

export function ReadingItem({ word, onset, rime, onComplete }: ReadingItemProps): JSX.Element {
  const [isListening, setIsListening] = useState(false);
  const { speak } = useTTS();
  const { listen, transcript, isListening: sttListening } = useSTT();
  const { scoreWord } = useWordScoring();
  const updateProgress = useGameStore((state) => state.updateProgress);

  useEffect(() => {
    // Speak the word prompt
    speak(`Say: ${word}`);
  }, [word, speak]);

  const handleListen = async () => {
    setIsListening(true);
    const result = await listen({ timeout: 5000 });
    if (result.transcript) {
      const score = scoreWord(result.transcript, word, onset, rime);
      updateProgress({ word, score });
      onComplete(score);
    }
    setIsListening(false);
  };

  return (
    <Box sx={{ p: 3, textAlign: 'center' }}>
      <Typography variant="h3" sx={{ mb: 4, fontSize: '2.5rem' }}>
        {word}
      </Typography>
      <Button
        variant="contained"
        size="large"
        onClick={handleListen}
        disabled={isListening}
        sx={{ minHeight: '44px', minWidth: '120px' }}
      >
        {isListening ? <CircularProgress size={24} /> : 'Listen'}
      </Button>
    </Box>
  );
}
```

### Example: React Hook in TypeScript

```typescript
// File: src/hooks/useTTS.ts
// This is a React hook written in TypeScript

import { useCallback, useRef } from 'react';

interface TTSOptions {
  rate?: number;
  pitch?: number;
  volume?: number;
}

interface UseTTSResult {
  speak: (text: string, options?: TTSOptions) => Promise<void>;
  pause: () => void;
  resume: () => void;
  stop: () => void;
  isSpeaking: boolean;
}

export function useTTS(): UseTTSResult {
  const utteranceRef = useRef<SpeechSynthesisUtterance | null>(null);
  const [isSpeaking, setIsSpeaking] = useState(false);

  const speak = useCallback(async (text: string, options?: TTSOptions): Promise<void> => {
    return new Promise((resolve, reject) => {
      const utterance = new SpeechSynthesisUtterance(text);
      utterance.lang = 'en-US';
      utterance.rate = options?.rate ?? 0.95;
      utterance.pitch = options?.pitch ?? 1.0;
      utterance.volume = options?.volume ?? 1.0;

      utterance.onend = () => {
        setIsSpeaking(false);
        resolve();
      };

      utterance.onerror = (error) => {
        setIsSpeaking(false);
        reject(error);
      };

      utteranceRef.current = utterance;
      setIsSpeaking(true);
      window.speechSynthesis.speak(utterance);
    });
  }, []);

  const pause = useCallback(() => {
    window.speechSynthesis.pause();
    setIsSpeaking(false);
  }, []);

  const resume = useCallback(() => {
    window.speechSynthesis.resume();
    setIsSpeaking(true);
  }, []);

  const stop = useCallback(() => {
    window.speechSynthesis.cancel();
    setIsSpeaking(false);
    utteranceRef.current = null;
  }, []);

  return { speak, pause, resume, stop, isSpeaking };
}
```

### Summary

* ✅ **Everything** = React components written in TypeScript
* ✅ **UI Components** = React (`.tsx` files) using Material UI
* ✅ **Business Logic** = React hooks (`.ts` files)
* ✅ **Utilities** = TypeScript functions (`.ts` files)
* ✅ **State** = Zustand with TypeScript types
* ✅ **Styling** = Material UI theme + custom CSS variables for brand colors

**The plan states**: "This project will use **TypeScript** throughout. All components, hooks, utilities, and state management will be written in TypeScript, using Material UI for UI components."

---

## Current Project Analysis

### Codebase Statistics

* **Current State**: Basic React + Vite skeleton
* **Total Files**: 4 source files
  * `src/main.jsx` - React entry point (~10 lines)
  * `src/App.jsx` - Basic app component (~20 lines)
  * `src/App.css` - Basic styles (~30 lines)
  * `src/index.css` - Global styles (~30 lines)
* **Dependencies**: React 18, Vite 5, basic React types
* **Architecture**: Minimal skeleton, no routing, no state management, no components
* **Backend**: None (fully client-side)

### Current Features

* ✅ Basic React app structure
* ✅ Vite build configuration
* ✅ Netlify configuration (netlify.toml)
* ❌ No routing
* ❌ No state management
* ❌ No components
* ❌ No speech integration
* ❌ No persistence
* ❌ No Material UI
* ❌ No TypeScript

### What Needs to Be Built

**Everything** - This is a greenfield project starting from a minimal skeleton.

---

## Strategy & Planning

### Technology Stack Decisions

#### Frontend Framework
* **Choice**: React 18 with TypeScript 5
* **Rationale**: Industry standard, excellent TypeScript support, large ecosystem
* **Build Tool**: Vite 5
* **Rationale**: Fast HMR, excellent TypeScript support, modern tooling

#### Component Library
* **Choice**: Material UI (MUI) v5
* **Rationale**: 
  - Comprehensive component set reduces development time
  - Excellent accessibility out of the box
  - Strong TypeScript support
  - Customizable theming system
  - Well-documented
* **Alternative Considered**: Building from scratch
* **Why Not**: Would add 40-50 hours of component development

#### State Management
* **Choice**: Zustand
* **Rationale**:
  - Lightweight (no boilerplate)
  - Excellent TypeScript support
  - Simple API for kid-friendly app
  - No provider wrapping needed
* **Alternative Considered**: Context API
* **Why Not**: Can become verbose with multiple contexts, performance concerns

#### Routing
* **Choice**: React Router v6
* **Rationale**: Industry standard, excellent TypeScript support, declarative routing

#### Speech APIs
* **Choice**: Web Speech API (native browser APIs)
  - `speechSynthesis` for TTS
  - `webkitSpeechRecognition` for STT
* **Rationale**: 
  - No external dependencies
  - No API keys needed
  - Works offline (TTS)
  - Privacy-friendly (local processing)
* **Limitations**: 
  - STT requires internet (Chrome)
  - Browser compatibility (Chrome/Safari primarily)

#### Pokémon Integration
* **Choice**: 
  - `react-pokemon` npm package for components
  - PokéAPI for data and sprite URLs
* **Rationale**: 
  - Established community components
  - Free, open-source API
  - No copyright concerns (using official API)
  - Home Assets Mode allows parent overrides

#### Testing
* **Choice**: Vitest + React Testing Library
* **Rationale**:
  - Vitest works seamlessly with Vite
  - React Testing Library for component testing
  - Fast test runner
  - TypeScript support

#### Styling
* **Choice**: Material UI theme + CSS custom properties
* **Rationale**:
  - MUI theme for component styling
  - CSS variables for brand colors (from PRD)
  - Allows Calm Mode color adjustments
  - Maintains design system consistency

### Material UI Theming Strategy

#### Theme Structure

```typescript
// src/theme/theme.ts
import { createTheme, ThemeOptions } from '@mui/material/styles';

// Brand colors from PRD
const brandColors = {
  cerulean: '#2E6AE6',    // Primary action buttons, links
  electric: '#FFD400',     // Capture/progress highlights
  leaf: '#2FA36A',        // Success, friendly "buddy"
  ember: '#FF7A21',       // Attention micro-bursts
  berry: '#7A5AF8',       // Secondary actions, badges
  ink900: '#0F172A',      // Primary text
  cloud100: '#F1F5F9',    // Card surfaces
  cloud50: '#F8FAFC',     // App background
  danger: '#E5484D',      // Errors
  success: '#22C55E',     // Correct feedback
};

// Base theme
const baseTheme = createTheme({
  palette: {
    primary: {
      main: brandColors.cerulean,
      contrastText: '#FFFFFF',
    },
    secondary: {
      main: brandColors.berry,
      contrastText: '#FFFFFF',
    },
    success: {
      main: brandColors.success,
    },
    error: {
      main: brandColors.danger,
    },
    background: {
      default: brandColors.cloud50,
      paper: brandColors.cloud100,
    },
    text: {
      primary: brandColors.ink900,
    },
  },
  typography: {
    fontFamily: 'Lexend, system-ui, sans-serif',
    fontSize: 22, // Base 22-24px from PRD
    h1: {
      fontSize: '3rem', // Reading targets 32-40px
      fontWeight: 600,
    },
    h2: {
      fontSize: '2.5rem',
      fontWeight: 600,
    },
    button: {
      textTransform: 'none', // Material UI default is uppercase
      fontSize: '1rem',
      fontWeight: 500,
    },
  },
  components: {
    MuiButton: {
      styleOverrides: {
        root: {
          borderRadius: '8px', // Pill buttons from PRD
          minHeight: '44px',    // Accessibility: 44px+ tap targets
          padding: '0.6em 1.2em',
        },
      },
    },
    MuiCard: {
      styleOverrides: {
        root: {
          borderRadius: '8px',
          backgroundColor: brandColors.cloud100,
        },
      },
    },
    MuiChip: {
      styleOverrides: {
        root: {
          borderRadius: '16px', // Pill shape
        },
      },
    },
  },
});

// Calm Mode theme (reduced saturation, darker fills)
const calmTheme = createTheme({
  ...baseTheme,
  palette: {
    ...baseTheme.palette,
    primary: {
      main: '#1E4A8E', // ~25% saturation reduction, ~8% darker
    },
    secondary: {
      main: '#5A3FA8', // Adjusted for Calm Mode
    },
  },
});

export { baseTheme, calmTheme, brandColors };
```

#### CSS Custom Properties Integration

```css
/* src/styles/design-tokens.css */
:root {
  /* Brand colors as CSS variables for easy Calm Mode adjustments */
  --brand-cerulean: #2E6AE6;
  --brand-electric: #FFD400;
  --brand-leaf: #2FA36A;
  --brand-ember: #FF7A21;
  --brand-berry: #7A5AF8;
  --ink-900: #0F172A;
  --cloud-100: #F1F5F9;
  --cloud-50: #F8FAFC;
  --danger: #E5484D;
  --success: #22C55E;
}

[data-calm-mode="true"] {
  /* Calm Mode: reduce saturation ~25%, darken ~8% */
  --brand-cerulean: #1E4A8E;
  --brand-electric: #CCAA00;
  --brand-leaf: #1F7A4A;
  --brand-ember: #CC6219;
  --brand-berry: #5A3FA8;
}
```

#### Component Theming Pattern

```typescript
// Using theme in components
import { useTheme, Box } from '@mui/material';

function MyComponent() {
  const theme = useTheme();
  
  return (
    <Box
      sx={{
        backgroundColor: theme.palette.primary.main,
        color: theme.palette.primary.contrastText,
        // Can also use CSS variables
        borderColor: 'var(--brand-electric)',
      }}
    >
      Content
    </Box>
  );
}
```

### Library Choices & Rationale

| Library | Version | Purpose | Rationale |
|---------|---------|---------|-----------|
| React | ^18.2.0 | UI framework | Industry standard, excellent TS support |
| TypeScript | ^5.0.0 | Type safety | Catches errors early, improves DX |
| Vite | ^5.0.8 | Build tool | Fast HMR, excellent TS support |
| Material UI | ^5.14.0 | Component library | Comprehensive, accessible, themable |
| Zustand | ^4.4.0 | State management | Lightweight, simple API, great TS support |
| React Router | ^6.20.0 | Routing | Industry standard, declarative |
| react-pokemon | Latest | Pokémon components | Community component for creature display |
| Vitest | ^1.0.0 | Testing | Fast, Vite-native, TS support |
| @testing-library/react | ^14.0.0 | Component testing | Best practices for React testing |

### File Structure

```
quest-app/
├── public/
│   ├── icons/              # PWA icons
│   └── manifest.json       # PWA manifest
├── src/
│   ├── components/         # React components
│   │   ├── game/          # Game-specific components
│   │   │   ├── ReadingItem.tsx
│   │   │   ├── DiceCheck.tsx
│   │   │   ├── FeedbackDisplay.tsx
│   │   │   ├── ProgressIndicator.tsx
│   │   │   └── RewardAnimation.tsx
│   │   ├── character/     # Character creation
│   │   │   ├── CharacterCreator.tsx
│   │   │   ├── OnScreenKeyboard.tsx
│   │   │   ├── BuddyDisplay.tsx
│   │   │   └── CharacterStep.tsx
│   │   ├── rewards/       # Rewards system
│   │   │   ├── StarDisplay.tsx
│   │   │   ├── StickerCollection.tsx
│   │   │   ├── BadgeShowcase.tsx
│   │   │   ├── HabitatView.tsx
│   │   │   └── CreatureDisplay.tsx
│   │   ├── ui/            # Reusable UI components
│   │   │   ├── Button.tsx      # Wraps MUI Button
│   │   │   ├── Card.tsx        # Wraps MUI Card
│   │   │   ├── Modal.tsx        # Wraps MUI Dialog
│   │   │   └── Chip.tsx         # Wraps MUI Chip
│   │   └── layout/        # Layout components
│   │       ├── Layout.tsx
│   │       ├── Navigation.tsx
│   │       └── AccessibilityAnnouncer.tsx
│   ├── pages/             # Page components
│   │   ├── WelcomePage.tsx
│   │   ├── CharacterCreatorPage.tsx
│   │   ├── GamePage.tsx
│   │   ├── PlacementTestPage.tsx
│   │   ├── ParentDashboardPage.tsx
│   │   └── SettingsPage.tsx
│   ├── hooks/              # React hooks
│   │   ├── useTTS.ts
│   │   ├── useSTT.ts
│   │   ├── useWordScoring.ts
│   │   ├── useDiceCheck.ts
│   │   ├── useSessionTimer.ts
│   │   ├── useFrustrationDetection.ts
│   │   ├── useCalmMode.ts
│   │   ├── usePokemonAPI.ts
│   │   ├── useHomeAssets.ts
│   │   └── usePersistence.ts
│   ├── utils/              # Pure TypeScript utilities
│   │   ├── wordScoring.ts
│   │   ├── numberParsing.ts
│   │   ├── sessionTimer.ts
│   │   ├── masteryTracking.ts
│   │   └── placementTest.ts
│   ├── api/                # API clients
│   │   └── pokeapi.ts
│   ├── store/              # Zustand stores
│   │   ├── gameStore.ts
│   │   ├── characterStore.ts
│   │   ├── rewardsStore.ts
│   │   ├── settingsStore.ts
│   │   └── types.ts
│   ├── storage/            # Storage utilities
│   │   ├── localStorage.ts
│   │   └── indexedDB.ts
│   ├── theme/              # Material UI theme
│   │   ├── theme.ts
│   │   └── calmMode.ts
│   ├── styles/             # Global styles
│   │   ├── design-tokens.css
│   │   └── global.css
│   ├── data/               # Content data
│   │   ├── stages/         # Stage packs (JSON)
│   │   │   ├── stage1.json
│   │   │   ├── stage2.json
│   │   │   └── ...
│   │   └── placement-test.json
│   ├── App.tsx             # Root component
│   ├── main.tsx            # Entry point
│   └── vite-env.d.ts       # Vite type definitions
├── .gitignore
├── index.html
├── netlify.toml
├── package.json
├── tsconfig.json           # TypeScript config
├── vite.config.ts          # Vite config
└── vitest.config.ts        # Vitest config
```

### State Management Architecture

#### Normalized State Structure

Following the Yarny plan's "derive, don't duplicate" principle, state will be normalized:

```typescript
// src/store/types.ts
export interface GameState {
  // Current session
  session: {
    id: string;
    startTime: number;
    endTime: number | null;
    targetDuration: number; // 5-10 minutes randomized
    targetItems: number;     // ~10 items
    itemsCompleted: number;
    diceChecksCompleted: number;
    isActive: boolean;
  };
  
  // Current item
  currentItem: {
    word: string;
    onset: string;
    rime: string;
    stageId: number;
    itemIndex: number;
  } | null;
  
  // Progress tracking
  progress: {
    [stageId: number]: {
      items: {
        [word: string]: {
          attempts: number;
          correctCount: number;
          lastAttempt: number;
          mastered: boolean; // 3 correct across separate days
        };
      };
    };
  };
  
  // Mastery tracking
  mastery: {
    [word: string]: {
      correctDates: number[]; // Timestamps
      mastered: boolean;
    };
  };
}

export interface CharacterState {
  character: {
    id: string;
    name: string;
    buddyColor: string;
    starterType: string;
    hair: string;
    skinTone: string;
    outfit: string;
    createdAt: number;
  } | null;
}

export interface RewardsState {
  stars: number;
  stickers: string[]; // Sticker IDs
  badges: string[];   // Badge IDs
  treasure: {
    habitat: 'camp' | 'treehouse' | 'pond';
    upgrades: string[];
  };
  creatures: {
    [creatureId: string]: {
      level: number;
      evolved: boolean;
      pokemonId?: number; // PokéAPI ID if using default
    };
  };
  streaks: {
    current: number;
    longest: number;
    lastSessionDate: number;
  };
}

export interface SettingsState {
  stage: number | null; // Current stage, null if needs placement
  calmMode: {
    enabled: boolean;
    locked: boolean; // Parent lock
    autoEngage: boolean;
  };
  frustrationLog: {
    timestamp: number;
    cause: string;
    response: string;
  }[];
  metrics: {
    sessionsThisWeek: number;
    avgMinutesPerSession: number;
    completionRate: number;
    accuracyByStage: Record<number, number>;
    firstTryRate: number;
    avgResponseLatency: number;
    diceAccuracy: number;
  };
}
```

#### Store Structure

```typescript
// src/store/gameStore.ts
import { create } from 'zustand';
import { GameState } from './types';

interface GameStore extends GameState {
  // Actions
  startSession: () => void;
  endSession: () => void;
  completeItem: (word: string, score: 'exact' | 'near' | 'partial' | 'miss') => void;
  updateProgress: (data: { word: string; score: string }) => void;
  setCurrentItem: (item: GameState['currentItem']) => void;
}

export const useGameStore = create<GameStore>((set, get) => ({
  // Initial state
  session: {
    id: '',
    startTime: 0,
    endTime: null,
    targetDuration: 0,
    targetItems: 10,
    itemsCompleted: 0,
    diceChecksCompleted: 0,
    isActive: false,
  },
  currentItem: null,
  progress: {},
  mastery: {},
  
  // Actions
  startSession: () => {
    const targetDuration = Math.floor(Math.random() * 5 + 5) * 60 * 1000; // 5-10 min
    set({
      session: {
        id: `session-${Date.now()}`,
        startTime: Date.now(),
        endTime: null,
        targetDuration,
        targetItems: 10,
        itemsCompleted: 0,
        diceChecksCompleted: 0,
        isActive: true,
      },
    });
  },
  
  completeItem: (word, score) => {
    const state = get();
    // Update progress, mastery, etc.
    set({
      session: {
        ...state.session,
        itemsCompleted: state.session.itemsCompleted + 1,
      },
    });
  },
  
  // ... other actions
}));
```

#### Selectors Pattern

```typescript
// src/store/selectors.ts
// Derive views instead of storing nested objects

export const selectCurrentStageProgress = (stageId: number) => 
  (state: GameState) => {
    const stageProgress = state.progress[stageId] || { items: {} };
    return {
      totalWords: Object.keys(stageProgress.items).length,
      masteredWords: Object.values(stageProgress.items)
        .filter(item => item.mastered).length,
      accuracy: calculateAccuracy(stageProgress.items),
    };
  };

export const selectSessionProgress = (state: GameState) => {
  const { session } = state;
  const elapsed = session.isActive 
    ? Date.now() - session.startTime 
    : (session.endTime || session.startTime) - session.startTime;
  return {
    elapsed,
    progress: Math.min(
      session.itemsCompleted / session.targetItems,
      elapsed / session.targetDuration
    ),
    shouldEnd: session.itemsCompleted >= session.targetItems || 
                elapsed >= session.targetDuration,
  };
};
```

### Effort Estimation

| Phase | Description | LOE (Hours) | Priority |
|-------|-------------|-------------|----------|
| 1 | Foundation & Infrastructure | 16-20 | P1 |
| 2 | Speech Infrastructure | 20-28 | P1 |
| 3 | Dice Mechanics | 12-16 | P1 |
| 4 | Character Creation | 16-20 | P1 |
| 5 | Adaptive Systems & Calm Mode | 18-24 | P1 |
| 6 | Rewards & Economy | 20-28 | P1 |
| 7 | Content System & Placement Test | 24-32 | P1 |
| 8 | Home Assets Mode | 16-20 | P2 |
| 9 | PWA & Data Management | 12-16 | P2 |
| 10 | Parent Dashboard & Polish | 16-20 | P1 |
| 11 | Testing & QA | 20-28 | P1 |
| 12 | Deployment & Documentation | 8-12 | P1 |
| **Total** | | **180-240** | |

**Note**: Using Material UI reduces component development time by ~30-40 hours compared to building from scratch.

---

## Implementation Details

### Speech API Integration

#### TTS Wrapper Implementation

```typescript
// src/hooks/useTTS.ts
import { useCallback, useState, useRef, useEffect } from 'react';
import { useCalmMode } from './useCalmMode';

interface TTSOptions {
  rate?: number;
  pitch?: number;
  volume?: number;
}

export function useTTS() {
  const [isSpeaking, setIsSpeaking] = useState(false);
  const utteranceRef = useRef<SpeechSynthesisUtterance | null>(null);
  const { calmModeEnabled } = useCalmMode();

  const speak = useCallback(async (
    text: string, 
    options?: TTSOptions
  ): Promise<void> => {
    return new Promise((resolve, reject) => {
      // Cancel any existing speech
      window.speechSynthesis.cancel();

      const utterance = new SpeechSynthesisUtterance(text);
      utterance.lang = 'en-US';
      
      // Calm Mode: ~10% slower rate
      const baseRate = calmModeEnabled ? 0.85 : 0.95;
      utterance.rate = options?.rate ?? baseRate;
      utterance.pitch = options?.pitch ?? 1.0;
      utterance.volume = options?.volume ?? 1.0;

      utterance.onstart = () => setIsSpeaking(true);
      
      utterance.onend = () => {
        setIsSpeaking(false);
        utteranceRef.current = null;
        resolve();
      };

      utterance.onerror = (error) => {
        setIsSpeaking(false);
        utteranceRef.current = null;
        reject(error);
      };

      utteranceRef.current = utterance;
      window.speechSynthesis.speak(utterance);
    });
  }, [calmModeEnabled]);

  const pause = useCallback(() => {
    window.speechSynthesis.pause();
    setIsSpeaking(false);
  }, []);

  const resume = useCallback(() => {
    window.speechSynthesis.resume();
    setIsSpeaking(true);
  }, []);

  const stop = useCallback(() => {
    window.speechSynthesis.cancel();
    setIsSpeaking(false);
    utteranceRef.current = null;
  }, []);

  // Cleanup on unmount
  useEffect(() => {
    return () => {
      window.speechSynthesis.cancel();
    };
  }, []);

  return { speak, pause, resume, stop, isSpeaking };
}
```

#### STT Wrapper Implementation

```typescript
// src/hooks/useSTT.ts
import { useCallback, useState, useRef, useEffect } from 'react';

interface STTOptions {
  timeout?: number;
  continuous?: boolean;
  interimResults?: boolean;
}

interface STTResult {
  transcript: string;
  confidence: number;
}

export function useSTT() {
  const [isListening, setIsListening] = useState(false);
  const [transcript, setTranscript] = useState('');
  const recognitionRef = useRef<SpeechRecognition | null>(null);

  const listen = useCallback(async (
    options?: STTOptions
  ): Promise<STTResult> => {
    return new Promise((resolve, reject) => {
      if (!('webkitSpeechRecognition' in window)) {
        reject(new Error('Speech recognition not supported'));
        return;
      }

      const SpeechRecognition = (window as any).webkitSpeechRecognition;
      const recognition = new SpeechRecognition();
      
      recognition.continuous = options?.continuous ?? false;
      recognition.interimResults = options?.interimResults ?? false;
      recognition.lang = 'en-US';

      let finalTranscript = '';
      let timeoutId: NodeJS.Timeout;

      // Start beep (could use Web Audio API)
      // playBeep('start');

      recognition.onstart = () => {
        setIsListening(true);
        setTranscript('');
        
        // Timeout after 4-5 seconds
        const timeout = options?.timeout ?? 5000;
        timeoutId = setTimeout(() => {
          recognition.stop();
        }, timeout);
      };

      recognition.onresult = (event: SpeechRecognitionEvent) => {
        const last = event.results.length - 1;
        const result = event.results[last];
        finalTranscript = result[0].transcript.trim();
        setTranscript(finalTranscript);
      };

      recognition.onend = () => {
        setIsListening(false);
        clearTimeout(timeoutId);
        // Stop beep
        // playBeep('stop');
        
        // Noise guard: discard <200ms utterances
        if (finalTranscript.length > 0) {
          resolve({
            transcript: finalTranscript,
            confidence: 0.8, // webkitSpeechRecognition doesn't provide confidence
          });
        } else {
          reject(new Error('No speech detected'));
        }
      };

      recognition.onerror = (event: SpeechRecognitionErrorEvent) => {
        setIsListening(false);
        clearTimeout(timeoutId);
        reject(new Error(event.error));
      };

      recognitionRef.current = recognition;
      recognition.start();
    });
  }, []);

  const stop = useCallback(() => {
    if (recognitionRef.current) {
      recognitionRef.current.stop();
      setIsListening(false);
    }
  }, []);

  // Cleanup on unmount
  useEffect(() => {
    return () => {
      if (recognitionRef.current) {
        recognitionRef.current.stop();
      }
    };
  }, []);

  return { listen, stop, transcript, isListening };
}
```

### Word Scoring Implementation

```typescript
// src/utils/wordScoring.ts
import { levenshteinDistance } from './levenshtein';

export type WordScore = 'exact' | 'near' | 'partial' | 'miss';

export interface ScoringResult {
  score: WordScore;
  feedback: string;
  matchedPart?: 'onset' | 'rime' | 'both';
}

export function scoreWord(
  transcript: string,
  target: string,
  onset: string,
  rime: string
): ScoringResult {
  const normalizedTranscript = transcript.toLowerCase().trim();
  const normalizedTarget = target.toLowerCase().trim();

  // Exact match
  if (normalizedTranscript === normalizedTarget) {
    return {
      score: 'exact',
      feedback: `Perfect! You said "${target}" correctly.`,
    };
  }

  // Near match: Levenshtein distance ≤1 (length-aware)
  const distance = levenshteinDistance(normalizedTranscript, normalizedTarget);
  const maxLength = Math.max(normalizedTranscript.length, normalizedTarget.length);
  
  if (distance <= 1 && maxLength <= 5) {
    return {
      score: 'near',
      feedback: `Close! I heard "${transcript}". Try: ${target}`,
    };
  }

  // Partial match: onset or rime match
  const transcriptOnset = normalizedTranscript.substring(0, onset.length);
  const transcriptRime = normalizedTranscript.substring(
    normalizedTranscript.length - rime.length
  );

  if (transcriptOnset === onset.toLowerCase() && transcriptRime === rime.toLowerCase()) {
    return {
      score: 'partial',
      feedback: `Good start! I heard the beginning sound /${onset[0]}/.`,
      matchedPart: 'both',
    };
  } else if (transcriptOnset === onset.toLowerCase()) {
    return {
      score: 'partial',
      feedback: `Good start! I heard the beginning sound /${onset[0]}/.`,
      matchedPart: 'onset',
    };
  } else if (transcriptRime === rime.toLowerCase()) {
    return {
      score: 'partial',
      feedback: `Good ending! I heard the ending sound.`,
      matchedPart: 'rime',
    };
  }

  // Miss
  return {
    score: 'miss',
    feedback: `Try with me: /${onset[0]}/ ... /${rime}/ ... ${target}`,
  };
}

// Levenshtein distance implementation
function levenshteinDistance(str1: string, str2: string): number {
  const matrix: number[][] = [];
  
  for (let i = 0; i <= str2.length; i++) {
    matrix[i] = [i];
  }
  
  for (let j = 0; j <= str1.length; j++) {
    matrix[0][j] = j;
  }
  
  for (let i = 1; i <= str2.length; i++) {
    for (let j = 1; j <= str1.length; j++) {
      if (str2.charAt(i - 1) === str1.charAt(j - 1)) {
        matrix[i][j] = matrix[i - 1][j - 1];
      } else {
        matrix[i][j] = Math.min(
          matrix[i - 1][j - 1] + 1,
          matrix[i][j - 1] + 1,
          matrix[i - 1][j] + 1
        );
      }
    }
  }
  
  return matrix[str2.length][str1.length];
}
```

### Pokémon Integration Strategy

#### PokéAPI Client

```typescript
// src/api/pokeapi.ts
export interface Pokemon {
  id: number;
  name: string;
  sprites: {
    front_default: string;
    front_shiny?: string;
    other: {
      'official-artwork': {
        front_default: string;
      };
    };
  };
  types: Array<{
    type: {
      name: string;
    };
  }>;
}

const POKEAPI_BASE = 'https://pokeapi.co/api/v2';

export async function fetchPokemon(id: number): Promise<Pokemon> {
  const response = await fetch(`${POKEAPI_BASE}/pokemon/${id}`);
  if (!response.ok) {
    throw new Error(`Failed to fetch Pokémon ${id}`);
  }
  return response.json();
}

export async function fetchPokemonByName(name: string): Promise<Pokemon> {
  const response = await fetch(`${POKEAPI_BASE}/pokemon/${name.toLowerCase()}`);
  if (!response.ok) {
    throw new Error(`Failed to fetch Pokémon ${name}`);
  }
  return response.json();
}

// Cache in localStorage for offline support
const CACHE_KEY = 'pokeapi_cache';
const CACHE_TTL = 7 * 24 * 60 * 60 * 1000; // 7 days

export async function getCachedPokemon(id: number): Promise<Pokemon | null> {
  const cache = localStorage.getItem(CACHE_KEY);
  if (!cache) return null;
  
  const parsed = JSON.parse(cache);
  const cached = parsed[id];
  
  if (cached && Date.now() - cached.timestamp < CACHE_TTL) {
    return cached.data;
  }
  
  return null;
}

export async function cachePokemon(id: number, data: Pokemon): Promise<void> {
  const cache = localStorage.getItem(CACHE_KEY);
  const parsed = cache ? JSON.parse(cache) : {};
  
  parsed[id] = {
    data,
    timestamp: Date.now(),
  };
  
  localStorage.setItem(CACHE_KEY, JSON.stringify(parsed));
}
```

#### React Hook for Pokémon

```typescript
// src/hooks/usePokemonAPI.ts
import { useState, useEffect } from 'react';
import { fetchPokemon, getCachedPokemon, cachePokemon, Pokemon } from '../api/pokeapi';

export function usePokemon(pokemonId: number | null) {
  const [pokemon, setPokemon] = useState<Pokemon | null>(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    if (!pokemonId) {
      setPokemon(null);
      return;
    }

    const loadPokemon = async () => {
      setLoading(true);
      setError(null);

      try {
        // Check cache first
        const cached = await getCachedPokemon(pokemonId);
        if (cached) {
          setPokemon(cached);
          setLoading(false);
          return;
        }

        // Fetch from API
        const data = await fetchPokemon(pokemonId);
        await cachePokemon(pokemonId, data);
        setPokemon(data);
      } catch (err) {
        setError(err as Error);
      } finally {
        setLoading(false);
      }
    };

    loadPokemon();
  }, [pokemonId]);

  return { pokemon, loading, error };
}
```

### Content Data Models

```typescript
// src/data/types.ts
export interface StagePack {
  stageId: number;
  name: string;
  targets: Array<{
    phoneme: string;
    grapheme: string;
  }>;
  items: Array<{
    word: string;
    onset: string;
    rime: string;
  }>;
  review: string[]; // Words for review
  diceMoments: Array<{
    dc: number; // Difficulty class (2-5)
    onSuccess: 'bonus_star' | 'easier_next' | 'treasure';
    onFail: 'model_then_easy' | 'partial_progress';
  }>;
}

export interface PlacementTest {
  id: string;
  items: Array<{
    type: 'letter-sound' | 'cvc' | 'digraph' | 'blend';
    prompt?: string;
    word?: string;
    target?: string;
    display?: string;
    onset?: string;
    rime?: string;
    pattern?: string;
  }>;
  rules: {
    advanceAt: number; // 0.8 (80%)
    stayAt: number;    // 0.5 (50%)
  };
}

// Example stage pack structure
export const stage1Example: StagePack = {
  stageId: 1,
  name: 'End-K Baseline (Short A)',
  targets: [
    { phoneme: '/æ/', grapheme: 'a' },
  ],
  items: [
    { word: 'cat', onset: 'c', rime: 'at' },
    { word: 'map', onset: 'm', rime: 'ap' },
    { word: 'sad', onset: 's', rime: 'ad' },
    { word: 'bag', onset: 'b', rime: 'ag' },
    { word: 'jam', onset: 'j', rime: 'am' },
    // ... ~50-100 words total
  ],
  review: [],
  diceMoments: [
    { dc: 2, onSuccess: 'bonus_star', onFail: 'model_then_easy' },
  ],
};
```

### Persistence Strategy

#### localStorage Wrapper

```typescript
// src/storage/localStorage.ts
export class LocalStorage {
  static get<T>(key: string, defaultValue: T): T {
    try {
      const item = localStorage.getItem(key);
      if (item === null) return defaultValue;
      return JSON.parse(item) as T;
    } catch (error) {
      console.error(`Error reading localStorage key "${key}":`, error);
      return defaultValue;
    }
  }

  static set<T>(key: string, value: T): void {
    try {
      localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(`Error writing localStorage key "${key}":`, error);
    }
  }

  static remove(key: string): void {
    try {
      localStorage.removeItem(key);
    } catch (error) {
      console.error(`Error removing localStorage key "${key}":`, error);
    }
  }

  static clear(): void {
    try {
      localStorage.clear();
    } catch (error) {
      console.error('Error clearing localStorage:', error);
    }
  }
}

// Typed storage keys
export const STORAGE_KEYS = {
  CHARACTER: 'quest:character',
  PROGRESS: 'quest:progress',
  REWARDS: 'quest:rewards',
  SETTINGS: 'quest:settings',
  SESSION: 'quest:session',
} as const;
```

#### IndexedDB Wrapper

```typescript
// src/storage/indexedDB.ts
const DB_NAME = 'quest-app';
const DB_VERSION = 1;
const STORE_NAME = 'home-assets';

interface AssetRecord {
  id: string;
  slot: 'starter' | 'evolution' | 'encounter' | 'badge';
  file: File;
  dataUrl: string;
  timestamp: number;
}

export class IndexedDBStorage {
  private db: IDBDatabase | null = null;

  async open(): Promise<IDBDatabase> {
    if (this.db) return this.db;

    return new Promise((resolve, reject) => {
      const request = indexedDB.open(DB_NAME, DB_VERSION);

      request.onerror = () => reject(request.error);
      request.onsuccess = () => {
        this.db = request.result;
        resolve(this.db);
      };

      request.onupgradeneeded = (event) => {
        const db = (event.target as IDBOpenDBRequest).result;
        if (!db.objectStoreNames.contains(STORE_NAME)) {
          const store = db.createObjectStore(STORE_NAME, { keyPath: 'id' });
          store.createIndex('slot', 'slot', { unique: false });
        }
      };
    });
  }

  async saveAsset(asset: AssetRecord): Promise<void> {
    const db = await this.open();
    return new Promise((resolve, reject) => {
      const transaction = db.transaction([STORE_NAME], 'readwrite');
      const store = transaction.objectStore(STORE_NAME);
      const request = store.put(asset);

      request.onsuccess = () => resolve();
      request.onerror = () => reject(request.error);
    });
  }

  async getAsset(id: string): Promise<AssetRecord | null> {
    const db = await this.open();
    return new Promise((resolve, reject) => {
      const transaction = db.transaction([STORE_NAME], 'readonly');
      const store = transaction.objectStore(STORE_NAME);
      const request = store.get(id);

      request.onsuccess = () => resolve(request.result || null);
      request.onerror = () => reject(request.error);
    });
  }

  async getAllAssets(): Promise<AssetRecord[]> {
    const db = await this.open();
    return new Promise((resolve, reject) => {
      const transaction = db.transaction([STORE_NAME], 'readonly');
      const store = transaction.objectStore(STORE_NAME);
      const request = store.getAll();

      request.onsuccess = () => resolve(request.result);
      request.onerror = () => reject(request.error);
    });
  }

  async clearAll(): Promise<void> {
    const db = await this.open();
    return new Promise((resolve, reject) => {
      const transaction = db.transaction([STORE_NAME], 'readwrite');
      const store = transaction.objectStore(STORE_NAME);
      const request = store.clear();

      request.onsuccess = () => resolve();
      request.onerror = () => reject(request.error);
    });
  }
}
```

### Accessibility Implementation

#### Focus Management

```typescript
// src/components/layout/AccessibilityAnnouncer.tsx
import { useEffect } from 'react';
import { Box } from '@mui/material';

interface AccessibilityAnnouncerProps {
  message: string;
  priority?: 'polite' | 'assertive';
}

export function AccessibilityAnnouncer({ 
  message, 
  priority = 'polite' 
}: AccessibilityAnnouncerProps) {
  useEffect(() => {
    if (message) {
      const announcer = document.getElementById('a11y-announcer');
      if (announcer) {
        announcer.setAttribute('aria-live', priority);
        announcer.textContent = message;
        
        // Clear after announcement
        setTimeout(() => {
          if (announcer) announcer.textContent = '';
        }, 1000);
      }
    }
  }, [message, priority]);

  return (
    <Box
      id="a11y-announcer"
      role="status"
      aria-live="polite"
      aria-atomic="true"
      sx={{
        position: 'absolute',
        left: '-10000px',
        width: '1px',
        height: '1px',
        overflow: 'hidden',
      }}
    />
  );
}
```

#### Keyboard Navigation

```typescript
// src/hooks/useInputMode.ts
export type InputMode = 'reading' | 'dice' | 'menu' | 'textentry';

export function useInputMode(mode: InputMode) {
  useEffect(() => {
    const handleKeyDown = (event: KeyboardEvent) => {
      switch (mode) {
        case 'reading':
          // Only Space and Esc work
          if (event.key === ' ' || event.key === 'Escape') {
            // Handle pause/resume
          } else {
            event.preventDefault();
          }
          break;
          
        case 'dice':
          // Only 1-6 work, Esc cancels
          if (event.key === 'Escape') {
            // Cancel dice
            event.preventDefault();
          } else if (['1', '2', '3', '4', '5', '6'].includes(event.key)) {
            // Handle dice roll
          } else {
            event.preventDefault();
          }
          break;
          
        case 'textentry':
          // Only A-Z and Backspace
          if (event.key === 'Backspace') {
            // Handle backspace
          } else if (/^[a-z]$/i.test(event.key)) {
            // Handle letter
          } else {
            event.preventDefault();
          }
          break;
      }
    };

    window.addEventListener('keydown', handleKeyDown);
    return () => window.removeEventListener('keydown', handleKeyDown);
  }, [mode]);
}
```

### Performance Considerations

#### Code Splitting

```typescript
// src/App.tsx
import { lazy, Suspense } from 'react';
import { CircularProgress, Box } from '@mui/material';

// Lazy load pages
const WelcomePage = lazy(() => import('./pages/WelcomePage'));
const GamePage = lazy(() => import('./pages/GamePage'));
const ParentDashboardPage = lazy(() => import('./pages/ParentDashboardPage'));

function App() {
  return (
    <Suspense
      fallback={
        <Box sx={{ display: 'flex', justifyContent: 'center', p: 4 }}>
          <CircularProgress />
        </Box>
      }
    >
      <Router>
        {/* Routes */}
      </Router>
    </Suspense>
  );
}
```

#### Memoization

```typescript
// Memoize expensive components
export const ReadingItem = React.memo(({ word, onComplete }: ReadingItemProps) => {
  // Component implementation
}, (prevProps, nextProps) => {
  // Custom comparison
  return prevProps.word === nextProps.word;
});

// Memoize expensive calculations
const stageProgress = useMemo(() => {
  return calculateStageProgress(stageId, progress);
}, [stageId, progress]);
```

---

## Build Phases

### Phase 1: Foundation & Infrastructure
**LOE**: 16-20 hours | **Priority**: P1

#### Goals
- Establish core app structure with TypeScript
- Set up Material UI theme
- Implement routing
- Set up state management with Zustand
- Create persistence layer

#### Tasks
- [ ] Convert project to TypeScript
  - Add `tsconfig.json`
  - Convert `.jsx` to `.tsx`
  - Add type definitions
- [ ] Install core dependencies
  - Material UI (`@mui/material`, `@mui/icons-material`)
  - Zustand (`zustand`)
  - React Router (`react-router-dom`)
  - TypeScript types
- [ ] Set up Material UI theme
  - Create theme with brand colors
  - Set up Lexend font
  - Configure component overrides
  - Add CSS custom properties
- [ ] Set up routing structure
  - React Router
  - Route definitions for all pages
- [ ] Create Zustand stores
  - `gameStore.ts`
  - `characterStore.ts`
  - `rewardsStore.ts`
  - `settingsStore.ts`
  - `types.ts`
- [ ] Create storage utilities
  - `localStorage.ts` wrapper
  - `indexedDB.ts` wrapper (basic structure)
- [ ] Set up session timer logic
  - Random 5-10 minute selection
  - Item counting (~10 items)
- [ ] Create base layout components
  - `Layout.tsx`
  - `Navigation.tsx`
- [ ] Set up PokéAPI utilities
  - Basic API client
  - Cache structure

#### Deliverables
- TypeScript project structure
- Material UI theme configured
- Routing working
- State management set up
- Persistence layer ready
- Session timer logic

#### Acceptance Criteria
- App compiles with TypeScript (no errors)
- Material UI theme applies correctly
- Navigation between routes works
- State persists to localStorage
- Session timer randomizes 5-10 minutes

---

### Phase 2: Speech Infrastructure
**LOE**: 20-28 hours | **Priority**: P1

#### Goals
- Build TTS/STT wrappers
- Implement word scoring
- Create reading loop
- Add feedback system

#### Tasks
- [ ] Create `useTTS.ts` hook
  - speechSynthesis wrapper
  - Rate ~0.95, pitch neutral
  - Pause/resume with Space
  - Calm Mode rate adjustment
- [ ] Create `useSTT.ts` hook
  - webkitSpeechRecognition wrapper
  - Single-turn listening
  - Start/stop beeps
  - 4-5s timeout
  - One auto-reprompt
  - Noise guards
- [ ] Implement word scoring utilities
  - `wordScoring.ts` with Levenshtein
  - Exact/near/partial/miss logic
  - Onset/rime matching
- [ ] Create `ReadingItem.tsx` component
  - TTS prompt
  - STT listening
  - Score calculation
  - Feedback display
- [ ] Implement feedback system
  - Specific praise messages
  - Progress indicators
  - Model prompts
- [ ] Add mic permission handling
  - Request flow
  - Deny/revoke recovery
  - Error messages

#### Deliverables
- Working TTS/STT integration
- Word scoring algorithm
- Reading loop component
- Feedback system

#### Acceptance Criteria
- AC-SPEECH-01: Exact/near/partial scoring with distinct feedback
- TTS speaks clearly at correct rate
- STT handles timeouts and reprompts
- Empty/noise inputs ignored
- Mic permissions handled gracefully

---

### Phase 3: Dice Mechanics
**LOE**: 12-16 hours | **Priority**: P1

#### Goals
- Implement d6 checks
- Speech and keyboard input
- DC outcomes

#### Tasks
- [ ] Create `DiceCheck.tsx` component
- [ ] Implement dice input modes
  - Speech recognition (1-6, "one" through "six")
  - Keyboard input (1-6)
  - Fuzzy number parsing
- [ ] Create confirmation chip UI
- [ ] Implement DC system (2-5)
- [ ] Create success/failure outcomes
- [ ] Add dice scheduling logic
  - 2 per session
  - 3rd if session >8 min
- [ ] Implement input mode filtering
- [ ] Add debouncing (250ms)

#### Deliverables
- Working dice checks
- Speech and keyboard input
- DC outcomes

#### Acceptance Criteria
- AC-DICE-KEY-01: Keyboard 1-6 accepted; mis-keys ignored; Esc cancels
- AC-DICE-SAY-01: Speech parses "four/for/4" → 4 with confirmation
- Dice checks trigger at correct intervals
- Success/failure outcomes work

---

### Phase 4: Character Creation
**LOE**: 16-20 hours | **Priority**: P1

#### Goals
- Build character creator
- TTS support
- On-screen keyboard

#### Tasks
- [ ] Create character creator flow
  - 6 steps (buddy color, hair, skin, outfit, name, mic check)
- [ ] Implement TTS for each step
- [ ] Create `OnScreenKeyboard.tsx`
  - Large A-Z buttons (44px+)
  - Backspace support
  - "Random" option
- [ ] Add dice shortcut
- [ ] Implement navigation
- [ ] Create character data model
- [ ] Save character to localStorage

#### Deliverables
- Complete character creator
- TTS support
- Character persistence

#### Acceptance Criteria
- AC-CHAR-01: ≤6 choices per screen; TTS reads label on focus; Back always available
- All steps complete successfully
- Character saves and loads

---

### Phase 5: Adaptive Systems & Calm Mode
**LOE**: 18-24 hours | **Priority**: P1

#### Goals
- Frustration detection
- Calm Mode system
- Adaptive help

#### Tasks
- [ ] Create frustration detection system
  - Signal detection logic
- [ ] Implement adaptive responses
- [ ] Create Calm Mode system
  - Visual changes
  - Audio changes
- [ ] Implement auto-engage
- [ ] Add parent lock
- [ ] Create movement break component
- [ ] Add breathing exercises
- [ ] Implement local logging

#### Deliverables
- Frustration detection
- Calm Mode with auto-engage
- Adaptive help system

#### Acceptance Criteria
- AC-ADAPT-01: Trigger cause + response logged
- AC-CALM-01: Toggling updates visuals/audio immediately
- Frustration signals detected correctly
- Adaptive responses trigger appropriately

---

### Phase 6: Rewards & Economy
**LOE**: 20-28 hours | **Priority**: P1

#### Goals
- Build rewards system
- Creature evolution
- Progression tracking

#### Tasks
- [ ] Create rewards data model
- [ ] Implement star system
- [ ] Create sticker system
- [ ] Build badge system
- [ ] Create treasure/habitat system
- [ ] Implement creature evolution
  - Use react-pokemon
  - Fetch from PokéAPI
- [ ] Add streak system
- [ ] Create rewards UI components

#### Deliverables
- Complete rewards system
- Progression tracking
- Visual reward displays

#### Acceptance Criteria
- Stars awarded correctly
- Badges unlock at milestones
- Streaks tracked accurately
- Rewards persist

---

### Phase 7: Content System & Placement Test
**LOE**: 24-32 hours | **Priority**: P1

#### Goals
- Build stage packs
- Placement test
- Content loading

#### Tasks
- [ ] Create content data models
- [ ] Build stage pack loader
- [ ] Create placement test component
- [ ] Implement placement rules
- [ ] Create stage content for all 10 stages
- [ ] Implement mastery tracking
- [ ] Add sight word handling

#### Deliverables
- All 10 stage packs
- Working placement test
- Content loading system

#### Acceptance Criteria
- AC-PLACEMENT-01: Placement test under 2 minutes; clear stage output
- All stages load correctly
- Mastery tracking works

---

### Phase 8: Home Assets Mode
**LOE**: 16-20 hours | **Priority**: P2

#### Goals
- Asset import system
- Pokémon component integration

#### Tasks
- [ ] Integrate `react-pokemon`
- [ ] Set up PokéAPI integration
- [ ] Create Home Assets Mode UI
- [ ] Implement file import
- [ ] Create asset mapping system
- [ ] Implement IndexedDB storage
- [ ] Add asset loading system
- [ ] Create warning banner
- [ ] Implement "Clear Assets"
- [ ] Add asset validation

#### Deliverables
- Working asset import
- Asset mapping UI
- Secure local storage

#### Acceptance Criteria
- AC-IP-01: No imported files in build/network
- Assets import and display correctly
- Clear Assets wipes IndexedDB

---

### Phase 9: PWA & Data Management
**LOE**: 12-16 hours | **Priority**: P2

#### Goals
- Make app installable
- Offline capability
- Export/import

#### Tasks
- [ ] Create PWA manifest
- [ ] Implement service worker
- [ ] Add install prompt
- [ ] Create export/import system
- [ ] Implement offline detection
- [ ] Add update notifications

#### Deliverables
- Installable PWA
- Offline functionality
- Export/import working

#### Acceptance Criteria
- AC-PWA-01: Installs; runs offline; lesson packs cached
- Export creates valid JSON
- Import restores state

---

### Phase 10: Parent Dashboard & Polish
**LOE**: 16-20 hours | **Priority**: P1

#### Goals
- Parent metrics dashboard
- Settings page
- Final polish

#### Tasks
- [ ] Create parent dashboard
- [ ] Implement metrics collection
- [ ] Create settings page
- [ ] Add Parents' Guide
- [ ] Implement input mode filtering
- [ ] Add auto-save & recovery
- [ ] Implement destructive confirmations
- [ ] Final accessibility audit
- [ ] Performance optimization
- [ ] Cross-browser testing

#### Deliverables
- Complete parent dashboard
- All settings functional
- Polished, accessible app

#### Acceptance Criteria
- AC-KEY-01, AC-KEY-02, AC-KEY-03: Input mode filtering works
- AC-RECOVER-01: Resume at checkpoint after refresh
- All metrics display correctly
- Accessibility standards met

---

### Phase 11: Testing & QA
**LOE**: 20-28 hours | **Priority**: P1

#### Goals
- Comprehensive testing
- Bug fixes
- Performance validation

#### Tasks
- [ ] Mic permissions testing
- [ ] STT testing
- [ ] Dice edge case testing
- [ ] Calm Mode testing
- [ ] Session timer testing
- [ ] Home Assets testing
- [ ] Accessibility testing
- [ ] Cross-device testing
- [ ] Performance testing

#### Deliverables
- Test results documentation
- Bug fixes
- Performance report

#### Acceptance Criteria
- All AC-* criteria met
- No critical bugs
- Performance acceptable

---

### Phase 12: Deployment & Documentation
**LOE**: 8-12 hours | **Priority**: P1

#### Goals
- Production deployment
- Complete documentation

#### Tasks
- [ ] Final Netlify configuration
- [ ] Create deployment docs
- [ ] Write user documentation
- [ ] Create developer documentation
- [ ] Set up monitoring
- [ ] Final production checks

#### Deliverables
- Production deployment
- Complete documentation
- Monitoring setup

#### Acceptance Criteria
- App accessible at quest.lindsaybrunner.com
- All documentation complete
- Privacy requirements met

---

## Project Management

### Risk Factors

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Web Speech API browser compatibility | High | Medium | Test early, provide fallback messaging |
| STT accuracy in noisy environments | High | High | Implement robust error handling, reprompts |
| PokéAPI rate limiting | Medium | Low | Implement caching, respect rate limits |
| IndexedDB storage limits | Medium | Low | Monitor storage usage, provide clear warnings |
| Material UI bundle size | Low | Low | Use tree-shaking, code splitting |
| TypeScript learning curve | Low | Low | Start simple, incrementally add types |

### Decision Points

1. **State Management Library**
   - **Decision**: Zustand
   - **Trigger**: If state becomes too complex, consider Redux Toolkit
   - **Criteria**: >10 stores, complex async flows

2. **Pokémon Asset Strategy**
   - **Decision**: PokéAPI + react-pokemon by default, Home Assets for overrides
   - **Trigger**: If PokéAPI becomes unreliable, consider pre-caching specific Pokémon
   - **Criteria**: API downtime >24 hours, rate limiting issues

3. **Calm Mode Implementation**
   - **Decision**: CSS variable adjustments + theme switching
   - **Trigger**: If performance issues, consider CSS-in-JS alternative
   - **Criteria**: Re-renders >100ms, janky animations

4. **Testing Strategy**
   - **Decision**: Vitest + React Testing Library for unit/component tests
   - **Trigger**: If E2E testing needed, consider Playwright
   - **Criteria**: Complex user flows, cross-browser requirements

### Success Criteria

#### Functional Requirements
- ✅ All acceptance criteria (AC-*) met
- ✅ All 10 stages implemented and functional
- ✅ Placement test completes in <2 minutes
- ✅ Speech recognition works reliably
- ✅ Calm Mode toggles immediately
- ✅ Rewards system tracks progress correctly
- ✅ Home Assets Mode stores files locally only

#### Performance Requirements
- ✅ Initial load <3 seconds
- ✅ Time to first interaction <1 second
- ✅ Smooth 60fps animations
- ✅ No memory leaks in long sessions
- ✅ Bundle size <500KB (gzipped)

#### Accessibility Requirements
- ✅ WCAG 2.1 AA compliance
- ✅ All interactive elements 44px+ tap targets
- ✅ Keyboard navigation works throughout
- ✅ Screen reader compatible
- ✅ Color contrast ≥4.5:1

#### Privacy Requirements
- ✅ No external analytics
- ✅ No data sent to third parties
- ✅ All data stored locally
- ✅ Export/import functionality works
- ✅ Home Assets never leave device

### Testing Strategy

#### Unit Tests
- Word scoring algorithms
- Number parsing
- Session timer logic
- Mastery tracking
- Placement test scoring

#### Component Tests
- ReadingItem component
- DiceCheck component
- CharacterCreator component
- All UI components

#### Integration Tests
- Speech API integration
- PokéAPI integration
- Persistence layer
- State management

#### E2E Tests (if needed)
- Complete character creation flow
- Full game session
- Placement test flow
- Parent dashboard access

### Performance Budgets

| Metric | Target | Measurement |
|--------|--------|-------------|
| Initial bundle size | <500KB gzipped | Lighthouse |
| Time to First Byte | <500ms | Network tab |
| First Contentful Paint | <1.5s | Lighthouse |
| Time to Interactive | <3s | Lighthouse |
| Largest Contentful Paint | <2.5s | Lighthouse |
| Cumulative Layout Shift | <0.1 | Lighthouse |
| Memory usage (idle) | <50MB | Chrome DevTools |
| Memory usage (active session) | <100MB | Chrome DevTools |

---

## Reference

### Resources

- [PRD: Pocket Phonics Quest](./chatgpt_project_overview.md)
- [Pokémon Graphics Integration Guide](./pokemon_graphics_integration.md)
- [Material UI Documentation](https://mui.com/)
- [Zustand Documentation](https://zustand-demo.pmnd.rs/)
- [Web Speech API MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API)
- [PokéAPI Documentation](https://pokeapi.co/docs/v2)

### Technical Details

#### TypeScript Configuration

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

#### Vite Configuration

```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
  build: {
    outDir: 'dist',
    sourcemap: true,
  },
});
```

### Changelog

* **2025-01-XX**: Initial detailed plan created
  * Added comprehensive project structure
  * Added Material UI theming strategy
  * Added implementation details with code examples
  * Added effort estimation
  * Added risk factors and decision points
  * Added testing strategy and performance budgets

### Next Steps

1. Review and approve this plan
2. Set up TypeScript configuration
3. Install Material UI and core dependencies
4. Begin Phase 1: Foundation & Infrastructure

---

## Contact & Support

For questions or updates to this plan, please document changes in the changelog.

© 2025 Pocket Phonics Quest. Built for quest.lindsaybrunner.com.

