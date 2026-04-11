# PR #12 Review — Add showcase: activity logging, events tab, component gallery

**Reviewer:** Pincer 🦀
**Date:** 2026-04-11
**Verdict:** Approve

---

## Summary

Restructures hello_world from a single-page module to a 3-tab showcase demonstrating PhoenixKit patterns. 10 files, +1706/-46 lines.

1. **Overview** — Module info + "Log demo event" button with canonical activity logging pattern
2. **Events** — Infinite-scroll activity feed with graceful degradation
3. **Components** — Live showcase of PhoenixKit core UI components with copy-paste snippets

---

## What Works Well

1. **Activity logging pattern is textbook** — `Code.ensure_loaded?/1` guard, rescue wrapper, `actor_uuid` extraction. This is the reference implementation other modules should follow.
2. **Graceful degradation** — `EventsLive` handles the case where `PhoenixKit.Activity` isn't loaded. Shows "Activity logging is not available" instead of crashing.
3. **Path helpers centralized** — `Paths` module routes everything through `PhoenixKit.Utils.Routes.path/1`. Clean.
4. **Tab structure** — Parent tab with `match: :prefix` + subtabs with `parent:` reference. Correct pattern for multi-tab modules.
5. **Components showcase** — Useful developer reference. Copy-paste snippets are a nice touch.
6. **AGENTS.md update** — Thorough documentation of the activity logging pattern with rules and code example.

---

## Issues and Observations

### Style (minor)

1. **`actor_uuid/1` pattern** — Same helper defined in `HelloLive`. Not duplicated across many files like in catalogue, so less of a concern here. But if more LiveViews are added, extract it.

2. **ComponentsLive is 905 lines** — It's a showcase file so it's inherently large, but worth noting. Could be split by component category in the future.

3. **Changelog already has 0.1.3 entry** — Version was bumped in the PR. We'll need to verify it matches what we publish.

---

## Post-Review Status

No blockers. Clean showcase PR that serves as a reference implementation for module developers.
