# CrossLens Project Current State
**Last Updated:** 2026-09-23  
**Repository:** /Users/matt/CrossLens  
**Branch:** main  
**Status:** V-0.0.5 stabilization in progress

## Project Overview

CrossLens is a native Android app (Kotlin, Jetpack Compose, Material 3) that demonstrates cross-source news comparison with an editorial focus on design quality.

**Core Concept:** Compare how different news sources frame the same story, showing differences in emphasis, language, and perspective.

## Current Milestone: V-0.0.5

**Focus:** Visual branding completion
- Launcher icon ✅ (integrated, tested on Pixel)
- Editorial signature 🔄 (visibility fix in progress)

**Previous Milestones:**
- V-0.0.4: Editorial review workflow
- V-0.0.3: Free/Plus paywall and settings
- V-0.0.1-0.0.2: Initial skeleton

## Architecture

**Stack:**
- Kotlin 1.9.24
- Android Gradle Plugin 8.5.2
- Gradle 8.9
- Compose BOM 2024.06.00
- Hilt for DI
- Room for local storage
- DataStore for preferences
- Navigation Compose
- MVVM pattern with ViewModels exposing immutable Flow-based UI state

**Module Structure:**
- Single app module
- Min API 29 (Android 10)
- Compile/Target API 34

**Key Patterns:**
- Mock-data-first (no network calls in demo)
- Repository pattern with mock/live interface
- Offline-capable by design
- Theme-aware components (Light/Dark/System)

## Design Requirements

**Critical:** Visual quality is a first-milestone requirement, not optional.

**Design System:**
- Editorial typography (serif display + sans body)
- Warm ivory/charcoal backgrounds
- Deep teal accent color
- Crossing-perspectives motif (offset panes + diagonal rule)
- Magazine-style composition
- Reduced motion support
- Full accessibility (TalkBack, large text, RTL)

**Visual Acceptance Gate:**
- Must capture screenshots of all 5 screens
- Must verify on physical device
- Must test Light/Dark themes
- Must test reduced motion
- Must verify crossing-perspectives motif is visible

## Current Work (2026-09-23)

**Problem:** Editorial signature's perspective panes were invisible (8-12% opacity)
**Solution:** Increased to 20-24% opacity, slightly larger panes
**Files Changed:**
- `EditorialSignature.kt` - visibility adjustments
- `EDITORIAL_SIGNATURE.md` - documentation update

**Build Status:**
- Tests: ✅ Passing
- Lint: ✅ Clean
- APK: ✅ Built (58MB)
- Device Test: ⏸️ Pending user verification on Pixel

## Key Files to Review Before Sessions

**Project Instructions:**
- `/Users/matt/CrossLens/CLAUDE.md` - Implementation rules, scope, quality gates
- `/Users/matt/CrossLens/README.md` - Product intent, domain contracts
- `/Users/matt/CrossLens/docs/ANDROID_BUILD_GUIDE.md` - Milestone definition, acceptance criteria
- `/Users/matt/CrossLens/docs/DESIGN_DIRECTION.md` - Visual requirements, design philosophy
- `/Users/matt/CrossLens/docs/CLAUDE_BUILD_RUNBOOK.md` - Build phases, verification steps

**Current State:**
- `/Users/matt/CrossLens/docs/BUILD_STATUS.md` - Toolchain, build status, test results
- `/Users/matt/CrossLens/docs/QUALITY_REPORT.md` - Audit results, findings
- `/Users/matt/CrossLens/docs/NEXT_SESSION_TODO.md` - Work in progress

**Release Notes:**
- `/Users/matt/CrossLens/RELEASE_NOTES_v0.0.5.md` - Current version changelog

## Mock Data Structure

**3 Stories:**
1. European Green Deal progress
2. Quantum computing breakthrough
3. Arctic research expedition

**6 Sources across 4 regions:**
- Europe: Le Monde (FR), The Times (UK)
- North America: New York Times (US)
- Middle East: Al Jazeera (Qatar)
- Asia: Asahi Shimbun (JP), The Hindu (India)

**Content Languages:** English, French, Arabic, Japanese
**Translations:** Labeled as demo translations (not claiming production localization)

## Quality Gates Remaining for V-0.0.5

1. **Visual Verification on Pixel:**
   - Home masthead shows visible crossing-perspectives motif ⏸️
   - Motif visible in loading/empty states ⏸️
   - Motif remains subtle and secondary to content ⏸️
   - Light/Dark theme adaptation works ⏸️

2. **Final Checks:**
   - TalkBack: decorative signature is silent ⏸️
   - Reduced motion: no excessive animation ⏸️
   - Settings: theme preference persists ⏸️

3. **Release Preparation:**
   - Update BUILD_STATUS.md ⏸️
   - Update RELEASE_NOTES_v0.0.5.md if needed ⏸️
   - Commit and push to main ⏸️
   - Create GitHub release (user will do this) ⏸️

## Common Patterns to Follow

**Before Implementation:**
1. Read CLAUDE.md for project rules
2. Read DESIGN_DIRECTION.md for visual requirements
3. Check BUILD_STATUS.md for current state
4. Review NEXT_SESSION_TODO.md for priorities

**During Implementation:**
- Small working increments
- Preserve unrelated work
- No speculative abstractions
- Test as you go
- Update documentation alongside code

**Before Completion:**
- Run tests: `./gradlew test`
- Run lint: `./gradlew :app:lintDebug`
- Build APK: `./gradlew assembleDebug`
- Update BUILD_STATUS.md with results
- Capture screenshots if UI changed
- Commit with descriptive message + co-author attribution

## Blocked Items

None currently. All tools and environment available.

## Technical Debt

**P2 (Deferred, not blocking):**
- Divider deprecation warnings (should use HorizontalDivider)
- Some screens use deprecated Compose APIs

**Not Debt, Out of Scope:**
- Network integration (mock-only is intentional)
- Google Play Billing (milestone boundary)
- Live content updates (future feature)
- Push notifications (future feature)
