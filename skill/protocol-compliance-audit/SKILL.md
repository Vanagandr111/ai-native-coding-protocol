---
name: protocol-compliance-audit
description: "Full audit of any file or project against the AI-Native Coding Protocol. Loads ai-native-coding-protocol as the baseline. Provides a step-by-step checklist, grep commands, and report template."
tags: [audit, compliance, protocol, code-review, quality]
depends_on: [ai-native-coding-protocol]
---

# Protocol Compliance Audit

**Depends on:** `ai-native-coding-protocol` (load it first)
**Applies to:** any project in any language (Python, JS/TS, Java, Go, C++)

---

## Contents

1. [Triggers](#1-triggers)
2. [Audit Procedure](#2-audit-procedure)
3. [Quick Grep Commands](#3-quick-grep-commands)
4. [Protocol Checks](#4-protocol-checks)
5. [Report Template](#5-report-template)
6. [Common Fixes](#6-common-fixes)
7. [What Not To Check](#7-what-not-to-check)
8. [Full Audit Example](#8-full-audit-example)

---

## 1. Triggers

Use this skill when the user says:

- "do a code review"
- "check against the protocol"
- "check code quality"
- "audit"
- "compliance check"
- "go through the checklist"
- "check against ai-native-coding-protocol"
- "do it" in the context of code inspection

Also use it automatically after bug fixes if more than two files were touched.

---

## 2. Audit Procedure

### Step 0 - Load Skills

```python
# Required:
skill_view(name='ai-native-coding-protocol')
skill_view(name='protocol-compliance-audit')  # this file

# If needed:
skill_view(name='bleed-stop')  # if patch() or write_file() is planned
```

### Step 1 - Collect Context

```bash
# 1. Which files changed?
git status --short

# 2. If there is no git repository, inspect files manually.
ls -la changed_files

# 3. How many lines are in each file?
wc -l changed_files

# 4. File structure.
grep -n "^def \|^class \|^# -- \|^# [A-Z]" file.py | head -40
```

### Step 2 - Run The Checklist (Section 4)

For each item:

1. Read the rule from `ai-native-coding-protocol` from memory.
2. Check the file for compliance.
3. Record PASS / FAIL / WARN.
4. For every FAIL, describe the concrete violation.

### Step 3 - Produce The Report (Section 5)

### Step 4 - Fix Violations (Section 6)

Rule: one violation = one patch. Do not combine five unrelated fixes into one patch/write_file operation.

### Step 5 - Recheck

After fixes, re-run the failed checks and confirm they became PASS.

---

## 3. Quick Grep Commands

```bash
# Function passports
grep -c "# intent:" file.py
grep -c "^def " file.py
# If # intent count is lower than def count, some functions are not covered.

# AI Navigation Layer
grep -c "# module-purpose:" file.py
grep -c "# entry-points:" file.py
grep -c "# main-flows:" file.py

# Temporary code
grep -n "TEMP\|FIXME\|TODO\|HACK\|XXX\|WORKAROUND" file.py

# Magic values (numbers except 0, 1, -1)
grep -nP '(?<!= )\b(?:[2-9]|[1-9]\d{2,})\b' file.py | grep -v "offset\|limit\|timeout\|max_\|min_\|port\|count\|rows\|cols\|line_\|step\|version\|padding\|gap\|index\|row_idx\|ci\."

# Hardcoded paths
grep -n '"/mnt\|"C:\\\\|"/tmp\|os\.environ\["\|environ\.get("' file.py

# Imports / dependencies
grep "^import \|^from " file.py

# Public functions
grep -n "^def [^_]" file.py

# Private functions
grep -n "^def _" file.py

# sidefx annotation
grep -n "# sidefx:" file.py

# typing / parameters
grep -n "^def .*," file.py | grep -v "# "  # functions with more than one parameter
```

---

## 4. Protocol Checks

### #0 - Skill Loaded First

- **Check:** Was `skill_view(name='ai-native-coding-protocol')` called before changes?
- **PASS:** loaded before the first patch/write_file.
- **FAIL:** code editing started before loading the baseline protocol.
- **Fix:** load it now, check already changed code against the protocol, and record the violation in the report.

### #1 - Reuse First

- **Check:** Does a new function duplicate existing code? Are there similar functions by name or meaning?
- **PASS:** existing code was extended, or the new code is genuinely new.
- **FAIL:** a new function duplicates about 70% of an existing one.
- **Fix:** delete the new function and extend the existing one with a parameter/options object.

### #2 - Git Backup

- **Check:** `git status`; are there uncommitted changes? Was a stash/commit made before editing?
- **PASS:** commit or stash was made before edits, or there were no changes.
- **WARN:** git exists, edits exist, no stash/commit was made.
- **FAIL:** no git repository, edits exist, and no backup exists.
- **Fix:** `git stash push -m "pre-audit $(date +%Y%m%d_%H%M%S)"` or `cp file file.bak`.

### #3 - Anti-Duplication

- **Check:** Search for duplicated logical blocks longer than five lines.
- **PASS:** no duplication.
- **FAIL:** duplicated logical blocks exist.
- **Fix:** extract a function and parameterize it.

### #4 - Balanced Architecture

- **Check:** Is file size reasonable for the task?
  - <50 lines: probably too small for a separate file.
  - 50-200: helper or small module.
  - 200-1200: normal module.
  - >1200: consider splitting.
- **PASS:** size matches complexity.
- **WARN:** file is >1200 lines; consider refactoring.
- **Fix:** move unrelated functions into a separate file.

### #5 - Semantic Cluster

- **Check:** Do all functions in the file belong to one domain?
- **PASS:** one file = one domain.
- **FAIL:** LAMMPS, UI, HTTP, and DB logic are mixed in one file.
- **Fix:** split by domain.

### #6 - Architecture Limits

- **Check:** No 5-10 line microfiles? No layer mixing?
- **PASS:** structure is reasonable.
- **FAIL:** microfiles or layer mixing exist.

### #7 - AI-Readable Code

- **Check:** Stable patterns and consistent structure?
- **PASS:** style is uniform.
- **FAIL:** one file mixes snake_case and camelCase, or uses multiple docstring styles.
- **Fix:** normalize to one local style.

### #8 - File Structure

- **Check:** Does the file follow imports -> constants -> types -> config -> services -> helpers -> main -> exports?
- **PASS:** order is respected.
- **FAIL:** constants appear after functions, or imports appear mid-file.
- **Fix:** move sections into the expected order.

### #9 - Semantic Function Passport

- **Check:** Does every public function have a passport (`# intent:`, `# allies:`, `# sidefx:`, `# purity:`, `# errors:`)?
- **PASS:** every public function has a complete passport.
- **WARN:** private functions have no passport; acceptable for small utilities.
- **FAIL:** a public function has no passport.
- **Fix:** add a passport.

### #10 - Naming Protocol

- **Check:** `verb_noun` for functions, PascalCase for classes, UPPER_SNAKE for constants?
- **PASS:** names comply.
- **FAIL:** naming violations exist.
- **Fix:** rename.

### #11 - Explicit Separation

- **Check:** Is the sidefx annotation correct? Are side effects explicit?
- **PASS:** all side effects are explicit.
- **FAIL:** a function is marked pure but writes to a file/DB.
- **Fix:** correct the annotation or split the function.

### #12 - Typing Rules

- **Check:** More than three parameters should use a DTO/dataclass.
- **PASS:** functions use at most three parameters, or a DTO exists.
- **WARN:** more than three parameters without DTO; acceptable for legacy/helpers.
- **Fix:** create a dataclass/DTO.

### #13 - No Magic Values

- **Check:** Numbers except 0/1/-1, path strings, and timeouts.
- **PASS:** all values are named constants.
- **FAIL:** magic numbers or strings exist.
- **Fix:** extract constants.

### #14 - Dependency Discipline

- **Check:** Is every external dependency justified?
- **FAIL:** library imported for one small function that is easy to implement locally.
- **WARN:** optional dependency has no graceful fallback.
- **Fix:** add try/except fallback or remove the dependency.

### #15 - Fail Fast

- **Check:** Input validation? Clear errors? No silent failures?
- **PASS:** inputs are validated and errors are informative.
- **FAIL:** silent return without message, or unclear exceptions.
- **Fix:** add `raise ValueError(...)` or `print(...) + return`.

### #16 - Pure Function Preference

- **Check:** Are pure functions annotated correctly?
- **FAIL:** function is marked pure but reads/writes external state.
- **Fix:** correct the annotation or implementation.

### #17 - Single Abstraction Level

- **Check:** No SQL + HTTP + business logic inside one function?
- **PASS:** levels are separated.
- **FAIL:** abstraction levels are mixed.
- **Fix:** split into helper functions.

### #18 - Anti-Hardcode

- **Check:** No hardcoded paths, URLs, tokens, or timeouts?
- **PASS:** all external values come from parameters/config/PROJDIR.
- **FAIL:** hardcoded value exists.
- **Fix:** move it to a parameter or constant.

### #19 - Temporary Code

- **Check:** Any `# TEMP:`, `# FIXME:`, `# TODO:`, or similar blocks?
- **PASS:** none.
- **WARN:** TEMP exists with `# reason:` and `# remove-after:`.
- **FAIL:** TEMP exists without deletion condition.
- **Fix:** add a date/removal condition or remove it.

### #20 - AI Navigation Layer

- **Check:** Are `# module-purpose:`, `# entry-points:`, `# main-flows:`, `# related-modules:` present?
- **PASS:** all four fields exist.
- **WARN:** incomplete layer (2-3 fields).
- **FAIL:** none exist.
- **Fix:** add the navigation layer.

### #21 - Architectural Manifest

- **Check:** Does a large feature have a preliminary structure?
- **PASS:** manifest exists for large changes.
- **FAIL:** large feature without a manifest.
- **N/A:** small edits.

### #22 - Predictability Rule

- **Check:** Are similar tasks solved with the same pattern?
- **PASS:** consistent.
- **FAIL:** one place uses `if/else`, another uses `match/case` for the same logic.

### #23 - AI Memory Anchors

- **Check:** Are `# critical:`, `# architecture:`, `# related:` present?
- **PASS:** present.
- **FAIL:** absent in key files.
- **N/A:** small helpers.

---

## 5. Report Template

After the audit, return a report in this format:

```md
## AI-Native Coding Protocol Audit

**File(s):** path/to/file.py
**Git status:** clean | dirty | no-git

### FAIL (must fix)

| # | Item | Problem | Fixed? |
|---|------|---------|--------|
| 2 | Git backup | No stash before edits | yes / no |
| ... | ... | ... | ... |

### WARN (recommendations)

| # | Item | Note |
|---|------|------|
| ... | ... | ... |

### PASS (X of Y total)

| # | Item | Status |
|---|------|--------|
| 1 | Reuse first | PASS |
| ... | ... | PASS |

### Bleed-stop audit (if patch/write_file was used)

| Operation | Risk | Status |
|-----------|------|--------|
| patch() on ... | ... | PASS / WARN / FAIL |

### Summary

- PASS: X
- WARN: Y
- FAIL: Z (fixed: W)
- **Verdict:** clean / minor issues / changes required
```

---

## 6. Common Fixes

### Missing Passport (#9)

```python
# Add before the function:
# intent: <what it does>
# allies: <related names/callers>
# sidefx: none|io|state|db
# purity: pure|impure
# errors: <how it fails>
```

### Missing AI Navigation Layer (#20)

At the top of the file after the docstring, or at the start:

```python
# module-purpose: <module purpose>
# entry-points: <entry points>
# main-flows: <main flows>
# related-modules: <related modules>
```

### Magic Value (#13)

Replace a hardcoded number with a constant:

```python
# Constants section:
COLUMN_OFFSET = 65

# Code:
chr(COLUMN_OFFSET + ci)
```

### Silent Failure (#15)

Add a message:

```python
except ImportError:
    print('  MODULE: skipped (lib not available)')
    return
```

### Missing Git Backup (#2)

```bash
git stash push -m "pre-audit $(date +%Y%m%d_%H%M%S)"
# or
cp file.py file.py.bak
```

---

## 7. What Not To Check

- **Test files** - the protocol does not apply to pure test/TDD cycles.
- **Config files** - `.yaml`, `.json`, `.toml`.
- **Markdown/README** - the protocol is for code.
- **Generated code** - proto, swagger, graphql.
- **Files under 30 lines** - if they are lightweight helpers or shell scripts.
- **Pure tests** - `test_*.py` files without business logic.

---

## 8. Full Audit Example

Use a previous `lmp_helper.py` audit as a live example: all 23 items were checked with grep commands, fixes, and a final report.

Key lessons:

1. Load the skill before the first patch; otherwise #0 FAIL.
2. Check git status before editing; otherwise #2 FAIL.
3. Search for magic values manually; `chr(65 + ci)` is not obvious.
4. Passport field `# errors:` must describe real behavior, not desired behavior.
5. After each fix, run `git diff` to ensure nothing broke.
