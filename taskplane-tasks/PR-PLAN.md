# PR Plan — Original 8 PRs → split into 15+ PRs (one task per PR)

**Created:** 2026-05-02
**Last updated:** 2026-05-23 (TP-012 merged via PR #129 — 14/15 done, only TP-019 left)
**Status:** In progress — 14/15 tasks merged on `main`, 1 remaining

The original 8-PR grouping plan was used as a guide, but the user opted to
ship one task per PR for easier review. Two new tasks (TP-017, TP-018) were
added 2026-05-07; TP-015 was deprecated as `TASK_OBSOLETE`. Resulting
delivery shape: each task lands as its own focused `feat/*` branch off
`origin/main`.

## Progress

| # | PR | Task | Status |
|---|----|------|--------|
| 1 | [#114](https://github.com/chenxin-yan/crust/pull/114) | TP-006 (skills `agents` default) | ✅ Merged |
| 2 | [#115](https://github.com/chenxin-yan/crust/pull/115) | TP-008 (`didYouMeanPlugin` rename) | ✅ Merged |
| 3 | [#116](https://github.com/chenxin-yan/crust/pull/116) | TP-016 (command/subcommand aliases) | ✅ Merged |
| 4 | [#117](https://github.com/chenxin-yan/crust/pull/117) | TP-013 (prompts polymorphic `validate`) | ✅ Merged |
| 5 | [#118](https://github.com/chenxin-yan/crust/pull/118) | TP-014 (validate API alignment) + subsumed TP-015 | ✅ Merged |
| 6 | [#119](https://github.com/chenxin-yan/crust/pull/119) | TP-003 (`installSkillBundle`) | ✅ Merged |
| 7 | [#120](https://github.com/chenxin-yan/crust/pull/120) | TP-009 (`choices` + `hidden`) | ✅ Merged |
| 8 | [#121](https://github.com/chenxin-yan/crust/pull/121) | TP-004 (skillPlugin `customSkills`) | ✅ Merged |
| 9 | [#122](https://github.com/chenxin-yan/crust/pull/122) | TP-010 (completion plugin) | ✅ Merged |
| 10 | [#123](https://github.com/chenxin-yan/crust/pull/123) | TP-017 (extract `@crustjs/schema-utils`) | ✅ Merged |
| 11 | [#125](https://github.com/chenxin-yan/crust/pull/125) | TP-018 (store schemas + `field()` migration) | ✅ Merged |
| 12 | [#126](https://github.com/chenxin-yan/crust/pull/126) | TP-005 (`@crustjs/utils` package) | ✅ Merged |
| 13 | [#128](https://github.com/chenxin-yan/crust/pull/128) | TP-011 (consolidate type primitives) | ✅ Merged |
| 14 | [#129](https://github.com/chenxin-yan/crust/pull/129) | TP-012 (extend ValueType + `parse`) | ✅ Merged |
| 15 | — | TP-019 (lean up tests + comments) | ⏸ Pending (added 2026-05-10) |

> **Note (2026-05-07):** TP-015 (DX alignment demo + docs sweep) was
> deprecated as `TASK_OBSOLETE` after PR #118 carried its cross-package
> docs/demo deliverables in-flight. See
> `.pi/supervisor/scout-reports/post-pr118-tp015.md`.

### Staleness audits performed

| Date | Trigger | Reports | Findings |
|------|---------|---------|----------|
| 2026-05-06 | post-PR #116 (TP-016) | `tp-track-*.md` | TP-009/010 amended (alias-aware completion); others clean |
| 2026-05-07 | post-PR #118 (TP-014) | `post-pr118-*.md` | TP-015 obsolete; TP-011 amended (3rd `ValueType` copy in validate) |
| 2026-05-09 | post-PR #119 + #120 | `post-pr119-skills.md`, `post-pr120-plugins-core.md`, `post-merges-refactor.md`, `post-merges-schema.md` | TP-004 amended (API shape); TP-005 amended (export status); TP-012 amended (`choices` validation scope + validate surface drift); TP-010, TP-011, TP-017, TP-018 clean |

---

## Remaining PR Plan (one task per PR)

| Order | Tag | Task | Stacks on | Notes |
|-------|-----|------|-----------|-------|
| 1 | PR-Q | **TP-019** | merged main (TP-012 in #129) | M→L. Lean up tests + 6 textEdit.ts comments + **scrub ~73 TP-\* internal-tracker references from 26 source/test files, 2 changesets, and `progress.md`** (Step 6 added 2026-05-12 per operator request). Runs last so a single scrub pass catches comment leakage from all merged TP-\* work (incl. TP-012). No public surface change; no changeset. Review Level 1 (Plan only). |

---

## Landing Tracks (post-merge)

```
Remaining:
  1. PR-Q (TP-019)   ──→ ready (all blockers merged on main, incl. TP-012)
                        runs LAST so its TP-* scrub catches comment
                        leakage from every merged TP-* including TP-012
```

---

## Grouping Rationale

### Where multi-task PRs are justified

- **PR-B (TP-003+TP-004)** — same GitHub issue. TP-004 is the *only* caller of
  the public API TP-003 introduces. Shipping TP-003 alone leaves a dangling
  public entrypoint and produces a confusing two-line changelog.
- **PR-F (TP-009+TP-010)** — TP-009's additive `core` fields (`choices`,
  `hidden`) exist *purely* to feed TP-010. Decoupling exposes public types
  that no shipped code consumes.
- **PR-H (TP-013+TP-014+TP-015)** — coordinated "schema-in / typed-value-out"
  alignment across `validate` + `prompts`. PROMPTs explicitly frame TP-015 as
  "the visible payoff" of the alignment work. Splitting risks intermediate
  states where docs/demo don't match the shipped API.

### Where dependency chains are kept as separate PRs

- **PR-C separated from PR-B** — PR-B is already L+L with Plan-and-Code
  review on both halves. Adding a new published package would triple blast
  radius across 3 packages.
- **PR-D separated from PR-C** — PR-D mutates `core` + `store` public
  re-export surfaces. Different review concern than "create new package".

---

## Execution Notes

- Each PR's tasks should be batched together via `orch_start` and integrated
  via `orch_integrate mode=pr`.
- Independent PRs (A, B, E, H) can run as concurrent batches if desired,
  but Crust is a single-worktree repo — running them serially is simpler.
- Stacked PRs (B→C→D, E→F, then G) require the parent PR to merge to `main`
  before the child batch starts so `main` reflects the dep.

---

### Staleness recheck 2026-05-07 (post-TP-013 / PR #117 merge)

After PR #117 landed, audited the 9 remaining PROMPTs against the merged code:

- **TP-003, TP-004, TP-005, TP-009, TP-010, TP-011, TP-012:** no `packages/prompts` references. Unaffected.
- **TP-014:** explicitly references TP-013's polymorphic slot as a verification target. The 4 predictions in TP-014's PROMPT (lines 187, 331, 361, 382) all hold against the merged code:
  1. ✅ `packages/prompts/src/` has zero `@crustjs/validate` runtime imports
  2. ✅ `packages/prompts/package.json` declares `@standard-schema/spec ^1.1.0`
  3. ✅ `isStandardSchema` is inline in `packages/prompts/src/core/types.ts:142` (not imported)
  4. ✅ `apps/docs/content/docs/modules/prompts.mdx` documents the polymorphic shape (lines 15, 84, 105)
- **TP-015:** docs/demo task; references TP-013 acceptance only. No amendments needed.

**Verdict:** no staleness amendments needed. PR-H₂ (TP-014 → TP-015) can run as written. TP-014 is recommended as the next solo batch (largest remaining, has prior Amendment 1).

---

### Staleness recheck 2026-05-07 (post-TP-014 / PR #118 merge + follow-up `1678945`)

After PR #118 landed, audited the 8 remaining-on-paper PROMPTs:

- **TP-003, TP-004, TP-005** (skills track) — `NO_AMENDMENT_NEEDED`. Zero references to validate APIs; `@crustjs/skills` has no validate dep.
- **TP-009, TP-010** (plugins track) — `NO_AMENDMENT_NEEDED`. Pure core/plugins work; no validate touching.
- **TP-011** (refactor) — `NO_AMENDMENT_NEEDED`. The PROMPT explicitly defers validate's third `ValueType` copy; added Amendment 1 (2026-05-07) noting validate's new copy at `schema-types.ts:30` is in scope of a future cleanup task, not TP-011.
- **TP-012** (refactor) — `NO_AMENDMENT_NEEDED`. Explicit "Do NOT touch validate" already in scope.
- **TP-015** (docs/demo) — `TASK_OBSOLETE`. PR #118 already executed every Step 1 deliverable: `field()` factory examples in both `validate.mdx` and `store.mdx`; clean migration sections in both READMEs; no leftover `parsePromptValue`/`promptValidator`/`fieldSync` refs in any production doc; cross-links resolve. `.DONE` marker created with `disposition: TASK_OBSOLETE`.

**Net result:** PR-H₂ is fully closed (PR #118 alone). 7 pending PROMPTs remain (TP-003, TP-004, TP-005, TP-009, TP-010, TP-011, TP-012) split across 5 PRs (B, C, D, F, G). No source amendments needed for any of them.

Full audit reports archived under `.pi/supervisor/scout-reports/post-pr118-{skills,plugins,refactor,tp015}.md`.
