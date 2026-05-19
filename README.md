# AI-Native Coding Protocol

A reuse-first coding protocol for AI agents working inside real codebases.

The main failure mode of coding agents is not bad syntax. It is unnecessary code:
duplicate logic, parallel abstractions, tiny one-off files, hidden side effects, hardcoded behavior, and architecture drift.

This protocol gives an agent a simple default: understand the existing system before adding to it.

## Core Idea

New code is a fallback, not the default.

Priority order:

1. Reuse existing code.
2. Extend existing code.
3. Create new code.

Before writing new code, the agent should prove that the codebase does not already contain a good extension point.

## Protocol

### 1. Search First

Before creating a function, class, file, endpoint, component, command, or module, search for:

- similar names
- existing contracts
- related types
- call sites
- semantic neighbors
- intent comments
- aliases and domain terms

If existing code covers most of the task, extend it instead of creating a parallel implementation.

### 2. Duplicate Logic Is A Bug

Duplicated behavior makes future agent work worse. It splits the source of truth and trains the next change to patch the wrong place.

Allowed duplication:

- tests
- examples
- temporary migration code with a removal condition

Everything else should be shared, extended, or deleted.

### 3. Keep Modules Domain-Coherent

A file should represent a coherent domain, not an arbitrary size target.

Do not create a file for five lines of code.
Do not turn one file into a dumping ground.
Group related behavior together when it shares one responsibility.

### 4. Make Code Searchable By Agents

Public APIs and important modules should expose intent in a stable, searchable way.

Example:

```python
# intent: validate active user session before protected action
# allies: auth_check, session_guard, access_validation
# sidefx: none
# purity: pure
# errors: InvalidSessionError, ExpiredSessionError
def validate_user_session(session: Session) -> UserId:
    ...
```

The point is not comment volume. The point is navigation. Future agents should find the right code before inventing new code.

### 5. Prefer Predictable Shape

Use the same pattern for the same kind of problem across the project.

Recommended file order:

1. imports
2. constants
3. types, interfaces, models
4. config
5. services
6. helpers
7. main logic
8. exports

Break the order only when the local framework or codebase pattern already has a stronger convention.

### 6. Make Side Effects Explicit

Pull these out of hidden logic:

- config
- env reads
- cache
- async boundaries
- retry behavior
- IO
- database access
- HTTP calls
- state mutation

Pure functions are easier for both humans and agents to reuse.

### 7. No Magic Values

Move magic values into named constants, config, enums, or typed parameters:

- numbers
- strings
- paths
- URLs
- keys
- timeouts
- limits

Names are part of the system's memory.

### 8. Temporary Code Must Expire

Temporary code must carry its own deletion plan.

```python
# TEMP:
# reason: support old token format during migration
# remove-after: all mobile clients >= 4.2
```

Unmarked temporary code becomes permanent architecture.

## Before Large Changes

Write a small architecture manifest before implementing a large feature.

```md
## Architecture Manifest

Files:
- `auth/session.py`: session validation
- `auth/tokens.py`: token parsing and refresh
- `api/middleware.py`: request boundary

Flow:
request -> middleware -> session validation -> handler

Reuse:
- `validate_user_session`
- `TokenRefreshPolicy`
- `AuthError`
```

The manifest should be short. It exists to prevent architecture drift before it starts.

## Agent Instruction

Use this in `AGENTS.md`, `CLAUDE.md`, Cursor rules, Codex skills, or any agent system prompt:

```md
Before writing new code:
1. Search for existing behavior.
2. Prefer reuse over modification.
3. Prefer extension over creation.
4. Do not duplicate logic.
5. Keep files domain-coherent.
6. Make side effects explicit.
7. Add intent metadata to public APIs and important modules.
8. Verify with tests or explain why verification was not possible.
```

## Install

Copy `skill/skill.md` into your agent skills directory, or paste `AGENTS.md` into a repository root.

## Status

This is a living protocol, not a framework.

It should stay small enough to paste into an agent context and concrete enough to change real coding behavior.
