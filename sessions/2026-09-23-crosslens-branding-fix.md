# CrossLens V-0.0.5 Branding Visibility Fix
**Date:** 2026-09-23  
**Project:** CrossLens Android App  
**Session Focus:** Fix invisible crossing-perspectives motif in editorial signature

## Problem Statement

The V-0.0.5 launcher icon passed physical Pixel testing - it was visible, centered, and unclipped. However, the in-app editorial signature's "crossing-perspectives motif" (perspective panes) was NOT visible during physical device testing.

**Root cause:** The perspective panes had extremely low alpha values (0.08 and 0.12, or 8-12% opacity), making them essentially invisible on actual devices.

## What Was Implemented Previously

The `CrossLensSignature` component in `EditorialSignature.kt` included:
- CrossLens wordmark text
- Diagonal crossing rule (visible)
- Top and bottom perspective panes (invisible due to low alpha)

**Original pane specs:**
- Top pane: 0.08 alpha (primary color)
- Bottom pane: 0.12 alpha (tertiary color)
- Heights: Small 4dp, Medium 6dp, Large 8dp

## Solution Applied

Increased visibility of perspective panes while maintaining "subtle and secondary to content" design requirement:

**Alpha adjustments:**
- Top pane: 0.08 → 0.20 (2.5x more visible)
- Bottom pane: 0.12 → 0.24 (2x more visible)

**Height adjustments:**
- Small: 4dp → 6dp
- Medium: 6dp → 8dp
- Large: 8dp → 10dp

## Design Rationale

The design direction (`docs/DESIGN_DIRECTION.md`) requires:
- "subtle crossing rule as an original perspective motif"
- "Repeat it sparingly"
- Visual elements must be "secondary to content"

The fix balances these requirements:
- 20-24% opacity is subtle but visible
- Still uses theme-aware colors (primary/tertiary)
- Doesn't compete with content
- Makes the motif actually present as documented

## Files Changed

1. `app/src/main/java/com/crosslens/app/core/ui/components/EditorialSignature.kt`
   - Increased perspective pane alpha values
   - Increased perspective pane heights
   
2. `docs/EDITORIAL_SIGNATURE.md`
   - Updated documentation to reflect new opacity values (20-24%)
   - Added specific pane height measurements

## Build Results

✅ **Unit Tests:** All passed  
✅ **Lint:** 0 errors, 0 warnings  
✅ **Debug Build:** SUCCESS  
**APK:** 58MB at `app/build/outputs/apk/debug/app-debug.apk`

## Next Steps

1. User will install APK on physical Pixel device
2. Verify crossing-perspectives motif is now visible in:
   - Home screen masthead (Large signature)
   - Loading states (Medium signature)
   - Empty states (Small signature)
3. Confirm it remains "subtle and secondary to content"
4. If visible and appropriate: commit, push, and proceed with V-0.0.5 release
5. If still not visible or too prominent: adjust alpha/size again

## Key Decision

**Chose visibility over extreme subtlety.** The original 8-12% opacity was so subtle it was invisible on real devices. The design system calls for "subtle" not "invisible." A motif that can't be seen fails its purpose as a visual signature. 20-24% opacity provides subtle visual presence while remaining secondary to content.

## Architecture Notes

The signature component is well-designed:
- Theme-aware (adapts to Light/Dark/System themes)
- Accessible (silent to TalkBack via clearAndSetSemantics)
- Three sizes for different contexts
- Reusable across Home, Explore, and Comparison screens
- Separate Wordmark variant for section headers (no panes)

The problem was purely a visual calibration issue, not a structural one.
