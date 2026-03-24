# Code Review: PR #6 — Add routing scaffold, CSS scanning, and developer documentation

**Reviewed:** 2026-03-24
**Reviewer:** Claude (claude-opus-4-6)
**PR:** https://github.com/BeamLabEU/phoenix_kit_hello_world/pull/6
**Author:** Max Don (mdon)
**Head SHA:** 7ca88e82eb84ec7b4a727f9f8299122d399235c7
**Status:** Merged

## Summary

Three commits adding `css_sources/0` callback for Tailwind CSS scanning, a `Routes` module template with commented-out stubs for multi-page routing, and extensive README/AGENTS.md documentation covering route ordering, LayoutWrapper warnings, and clean rebuild tips. +240/-1 across 4 files.

## Issues Found

### 1. [OBSERVATION] Empty compiled module — FIXED
**File:** `lib/phoenix_kit_hello_world/routes.ex`
The `Routes` module is entirely commented out — compiles to an empty module with only a `@moduledoc`. No file-level signal explaining why.
**Fix:** Added a file-level comment explaining this is a template scaffold and how to activate it.
**Confidence:** 85/100

### 2. [NITPICK] Verbose `css_sources/0` docstring — FIXED
**File:** `lib/phoenix_kit_hello_world.ex` lines 184-193
The `@doc` restates what's already in the README. For a template that users copy, a one-liner is less noisy.
**Fix:** Trimmed to `@doc "OTP apps whose templates Tailwind should scan for CSS classes."`
**Confidence:** 70/100

### 3. [BUG - MEDIUM] `route_module` removed from defaults summary — FIXED
**File:** `lib/phoenix_kit_hello_world.ex` line 217
The line `#   def route_module, do: nil` was deleted from the optional callbacks summary at the bottom. Someone scanning for "what callbacks exist" now has an incomplete list.
**Fix:** Restored with a pointer comment: `# see Route module section above`.
**Confidence:** 90/100

### 4. [BUG - MEDIUM] Missing `public_routes/1` stub — FIXED
**File:** `lib/phoenix_kit_hello_world/routes.ex`
The module documents `public_routes/1` in its table and warns about catch-all route ordering, but doesn't include a commented-out stub. Only `generate/1` is stubbed — the function the docs warn against using for catch-alls.
**Fix:** Added `public_routes/1` stub with inline warning about route ordering.
**Confidence:** 95/100

### 5. [NITPICK] README TOC anchor — verified OK
**File:** `README.md` line 27
The `#tailwind-css-scanning-for-modules` anchor matches the heading at line 2278. No issue.
**Confidence:** 100/100

## What Was Done Well

- Thorough documentation of real footguns (route ordering, LayoutWrapper double-sidebar, CSS purging)
- Route module as opt-in scaffold with clear "you don't need this yet" messaging
- Strong commit messages explaining *why*, not just *what*
- `css_sources/0` design is clean — declaring OTP app names and letting the installer resolve paths

## Verdict

Approved with fixes — solid documentation-heavy PR. All four actionable issues have been resolved in follow-up edits.
