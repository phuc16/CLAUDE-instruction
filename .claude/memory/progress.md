# Progress & Decisions

## Decisions Made

### 2026-06-15: CLAUDE.md structure for a documentation-only repo
**Decision:** CLAUDE.md summarizes the two guides' purposes, the Memory Bank file table, and skill invocation interface — no build/test commands since there are none.
**Why:** `/init` scans codebase; a pure-markdown repo has no commands to document, so the value is in the architectural summary (what each file is for, how the skill works).

### 2026-06-15: Uppercase .MD extension preserved
**Decision:** New files (`CLAUDE.md`) use lowercase `.md`; existing guides keep uppercase `.MD` as found.
**Why:** Consistency within the repo — don't silently rename files that other tooling or links may reference.

### 2026-06-15: Memory Bank path changed to .claude/memory/
**Decision:** Memory files live in `.claude/memory/` instead of `memory/` at project root.
**Why:** Keeps Claude-specific files grouped under the existing `.claude/` convention; avoids polluting project root with a `memory/` folder that has no obvious purpose to newcomers.

### 2026-06-16: init gitignore prompt moved to end, bundled with confirm
**Decision:** Gitignore Y/n prompt is shown at step 7 (after all files created), bundled with the confirm message and review prompt — not as a separate early step 3.
**Why:** Model output this format naturally and it was better UX: user sees the full picture (initialized + review needed + gitignore choice) in one block rather than being interrupted mid-init.

### 2026-06-16: init output format encoded verbatim in skill
**Decision:** Step 7 specifies the exact output text so future runs reproduce the same format consistently.
**Why:** Without it, model improvises — sometimes better, sometimes worse. Locking it in ensures reproducibility.

### 2026-06-16: commit suggestion added to done: and wrapup
**Decision:** After `done:` and at wrapup Step 4, skill suggests a `git commit -m "..."` based on completed work. No annotation or label — just the command.
**Why:** Reduces friction of writing commit messages; model already has context of what changed.

### 2026-06-16: skill "start" must log task before acting
**Decision:** Added IMPORTANT block to skill.md — after briefing, if user pastes task/error, write to activeContext.md first and confirm before doing any work.
**Why:** Model was jumping into fixing a brew error instead of logging it as the current task. Skill needed an explicit gate.

### 2026-06-16: Separate files for multi-language READMEs
**Decision:** Use `README.md` (EN default) + `README.vi.md` (VI) per folder instead of a single file with language sections.
**Why:** Single-file approach with `<a name="">` anchors doesn't work — GitHub strips `name` attributes from `<a>` tags. Heading-based anchors scroll to the section but both languages show at once. Separate files avoid anchors entirely; click = navigate to other file.
**Alternatives rejected:** Single file with `## English` / `## Tiếng Việt` headings — both sections visible at same time, no clean way to hide one.

### 2026-06-16: shields.io badges for language switcher
**Decision:** Both language badges (EN + VI) are always visible at top of every README. Active language = blue, inactive = lightgrey.
**Why:** User wants both badges visible at all times (not just the "other" language). shields.io `flat-square` style matches GitHub markdown aesthetic.

## What Didn't Work

### 2026-06-16: Single-file language switching with GitHub anchors
`<a name="english">` anchors before headings — GitHub sanitizer strips `name` attribute, links do nothing. Heading-based anchors (`## English`) work for scrolling but both sections show simultaneously.
