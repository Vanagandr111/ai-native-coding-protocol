---
name: ai-native-coding-protocol
description: "Reuse-first coding protocol for AI agents: avoid duplication, extend existing code, keep architecture predictable. Source: https://github.com/Vanagandr111/ai-native-coding-protocol"
---

# AI-Native Coding Protocol (EN)

# 0.0. CRITICAL: LOAD THIS SKILL FIRST
# ⚠️ Must be loaded BEFORE any code modification (patch, write_file, refactoring).
# User explicitly expects: every public function gets passport (#9) and file gets
# AI navigation layer (#20) WHENEVER code is written or modified.
# Loading this skill post-factum (after making changes) = protocol VIOLATION.
# 
# Applies to: ANY Python refactoring, new function, file edit, or bugfix.
# Does NOT apply to: pure test/TDD cycles, config-only changes, README edits.

# 0. PRIORITY ORDER
1. reuse existing code
2. extend existing code
3. create new code

# 1. Core Development Law
Goal: avoid generating new code unnecessarily. New code is last resort.

Before creating anything:
- check existing code, allies/tags/intent
- reuse or extend first
Duplication is forbidden.

# 2. GIT BACKUP PROTOCOL
If git is available and the user allows commits, create a checkpoint before changes.
If commits are not allowed, inspect and report dirty state before editing.

Rules:
- 1 commit = 1 logical task
- no mixed changes
- commit message = short description of completed change
- always keep working state safe

# 3. Anti-Duplication Protocol
Before new function:
- check naming similarity
- allies/tags/intent
- semantic similarity
- existing contracts

If ≥70% match:
→ extend existing function, not create new one

# 4. Balanced Architecture Rule
Avoid:
- spaghetti code
- excessive micro-files
- unnecessary fragmentation

Principle:
module size must match task complexity.

# 5. Semantic Cluster Rule
1 file = 1 domain.

Allowed together:
- related functions
- types/classes/services
if same responsibility.

# 6. Architecture Limits
Avoid:
- mixing UI / business / infra
- files with only 5–10 lines

Prefer:
- 3–10 public methods per module
- single abstraction level per function
- predictable structure

# 7. AI-Readable Code
Optimize for AI understanding:
- stable patterns
- consistent structure
- repeatability
- predictability

# 8. File Structure (mandatory order)
1. imports
2. constants
3. types/interfaces/models
4. config
5. services
6. helpers
7. main logic
8. exports

# 9. Semantic Function Passport
Every public function/class:

# intent:
# allies:
# sidefx: none|io|db|http|state
# purity: pure|impure
# errors:

Optional:
- mutates:
- related:
- temp-reason / remove-after

# 10. Naming Protocol
Functions: verb_noun  
Classes: PascalCase  
Constants: UPPER_SNAKE_CASE  

Example:
validate_user_session()
fetch_active_orders()
generate_invoice_pdf()

# 11. Explicit Separation
Extract:
- config
- env
- cache
- async
- retry
- side effects
- state mutations

# 12. Typing Rules
>3 params → DTO/structure  
Domain values → typed (UserId, Email)

# 13. No Magic Values
No:
- numbers
- strings
- paths
- URLs
- keys
- timeouts

Use:
constants / config / enums

# 14. Dependency Discipline
Add dependency only if:
- complex problem
- major time saving
- not replaceable internally

# 15. Fail Fast Rule
Validate early, throw clear errors, no silent failures.

Doc:
PURPOSE / INPUT / OUTPUT / SIDE EFFECTS / DEPENDENCIES / ERRORS

# 16. Pure Function Preference
Prefer pure functions:
- no external state mutation
- deterministic output

# 17. Single Abstraction Level
No mixing:
- business logic
- SQL
- HTTP
- low-level code

# 18. Anti-Hardcode Rule
No hardcoding:
- time
- paths
- URLs
- tokens
- config

Use params/config/env.

# 19. Temporary Code
# TEMP:
# reason:
# remove-after:

# 20. AI Navigation Layer
# module-purpose:
# entry-points:
# main-flows:
# related-modules:

# 21. Architectural Manifest
Before large features:
1. define structure
2. define responsibilities
3. define relations
4. then implement

# 22. Predictability Rule
Same task → same pattern everywhere.

# 23. AI Memory Anchors
# critical: true
# architecture:
# related: