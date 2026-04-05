# Code Review: PR #11 — Add required_integrations/0 and integration_providers/0 to template

**Reviewed:** 2026-04-05
**Reviewer:** Claude (claude-sonnet-4-6)
**Author:** Max Don (mdon)
**Head SHA:** 1d3146407f9f72593db1f2a9c90662d5b9c6e638
**Status:** Merged

---

## Summary

Minimal, documentation-focused PR. Adds two new optional callbacks from PhoenixKit's Integrations system to the hello world template:

- `required_integrations/0` — lists integration provider keys the module depends on
- `integration_providers/0` — lists custom provider definitions the module contributes

**Diff:** +12 lines, 2 files. No behavioural changes. Both callbacks already had defaults in `PhoenixKit.Module`; this PR just surfaces them as commented-out stubs and adds them to the docs table and test suite.

---

## What Works Well

**1. Scope is tight.** Three discrete, non-overlapping concerns (module doc table, commented defaults, tests) each touched exactly once. No scope creep.

**2. Documentation kept in sync.** The `@moduledoc` callbacks table, the commented-out defaults block, and the test suite are all three updated in the same commit. A developer who reads any one of the three reference points gets the full picture.

**3. Example values in the comments are useful.** `# e.g., ["google"] or ["openrouter"]` gives readers a concrete mental model of what the list looks like — good for a template whose job is to be copied.

**4. Tests are honest.** The tests verify the actual default return value (`== []`) rather than just checking `is_list/1`. This would catch a future regression where the default changes to something non-empty.

**5. Commit message is clear.** Explains *what* changed and *why* (expose the Integrations system to module authors), and correctly scopes the change as documentation-only.

---

## Issues and Concerns

### 1. [NITPICK] Column alignment in the callbacks table is broken

**File:** `lib/phoenix_kit_hello_world.ex`, lines 72–73

The two new rows use fewer spaces after `No` than the surrounding rows, breaking the visual alignment of the third column:

```
| `migration_module/0`  | No        | Versioned migration coordinator module             |
| `required_integrations/0` | No   | Integration provider keys this module needs        |
| `integration_providers/0` | No   | Custom provider definitions to contribute          |
```

The `No` column is visually misaligned because the callback names are longer. The table still renders correctly in markdown (column widths are irrelevant to parsers), but it looks sloppy in raw source. Low priority — template users copy the functional parts, not the table.

**Confidence:** 90/100

---

### 2. [OBSERVATION] No guidance on what `integration_providers/0` should return

**File:** `lib/phoenix_kit_hello_world.ex`, line 213

The comment for `required_integrations` gives an example: `# e.g., ["google"] or ["openrouter"]`. The comment for `integration_providers` gives none: `# custom provider definitions`. A module author implementing a custom provider has no idea what shape a provider definition takes — is it a map? a struct? a keyword list?

This is acceptable in a template (the real docs live in `PhoenixKit.Module`), but it creates an asymmetry — one callback has a hint, the other doesn't. Either drop the example from `required_integrations` to be consistent, or add a brief shape hint to `integration_providers`.

**Confidence:** 75/100

---

### 3. [OBSERVATION] Tests live in `"optional callbacks have defaults"` describe block

**File:** `test/phoenix_kit_hello_world_test.exs`, lines 132–139

The two new tests are correctly placed in the `"optional callbacks have defaults"` describe block. This is the right call — these callbacks aren't implemented in the module body, so testing the default is the correct level of coverage. No action needed; noting it explicitly because a future reviewer might wonder why there's no `describe "required_integrations/0"` block like `permission_metadata` has.

**Confidence:** 100/100

---

## Verdict

**Approve.** Clean, minimal, well-scoped. Issue #1 (table alignment) is cosmetic and not worth a blocker. Issue #2 is a documentation quality suggestion that can be addressed in a later pass. No bugs, no regressions.
