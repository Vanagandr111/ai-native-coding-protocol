<p align="center">
  <br>
  <img src="https://img.shields.io/badge/Priority-Reuse%20%E2%86%92%20Extend%20%E2%86%92%20Create-4CAF50?style=for-the-badge" alt="Priority Order">
  <br><br>
</p>

<h1 align="center">
  ⚕ AI-Native Coding Protocol
</h1>

<p align="center">
  <em>A reuse-first coding protocol for AI agents working inside real codebases.</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/license-MIT-blue?style=flat-square" alt="License">
  <img src="https://img.shields.io/badge/status-living%20protocol-yellow?style=flat-square" alt="Status">
  <img src="https://img.shields.io/badge/agent-Claude%20%E2%80%A2%20Codex%20%E2%80%A2%20Cursor-8A2BE2?style=flat-square" alt="Agent Support">
</p>

<br>

---

## Why

The main failure mode of coding agents is **not bad syntax. It is unnecessary code:**

```
duplicate logic • parallel abstractions • tiny one-off files
hidden side effects • hardcoded behavior • architecture drift
```

This protocol gives an agent a simple default: **understand the existing system before adding to it.**

<br>

## Core Idea

> **New code is a fallback, not the default.**

```
1. Reuse   existing code    ← always try first
2. Extend  existing code    ← fallback
3. Create  new code         ← last resort
```

Before writing new code, the agent must prove that the codebase does not already contain a good extension point.

<br>

---

## Protocol

### 🔍 1. Search First

Before creating *anything* — function, class, file, endpoint, component, command, module:

```
• similar names
• existing contracts
• related types
• call sites
• semantic neighbors
• intent comments
• aliases and domain terms
```

If existing code covers **≥70%** of the task — extend, don't duplicate.

<br>

### ❌ 2. Duplicate Logic Is A Bug

Duplicated behavior splits the source of truth. It trains the next change to patch the wrong place.

**Allowed duplication only:**
- tests
- examples
- temporary migration code *with a removal condition*

Everything else → share, extend, or delete.

<br>

### 📦 3. Keep Modules Domain-Coherent

```
One file = one responsibility.
```

| Don't | Do |
|---|---|
| Create a file for 5 lines of code | Group related behavior together |
| Turn one file into a dumping ground | 3–10 public methods per module |

<br>

### 🧭 4. Make Code Searchable By Agents

Public APIs and important modules expose intent in a **stable, searchable** way:

```python
# intent: validate active user session before protected action
# allies: auth_check, session_guard, access_validation
# sidefx: none
# purity: pure
# errors: InvalidSessionError, ExpiredSessionError
def validate_user_session(session: Session) -> UserId:
    ...
```

> The point is not comment volume. The point is **navigation**.  
> Future agents should find the right code *before* inventing new code.

<br>

### 📐 5. Prefer Predictable Shape

Same task → same pattern. Everywhere.

**Recommended file order:**

```
1. imports
2. constants
3. types / interfaces / models
4. config
5. services
6. helpers
7. main logic
8. exports
```

<br>

### 👁 6. Make Side Effects Explicit

Pull these out of hidden logic:

```
config  •  env reads  •  cache  •  async boundaries
retry   •  IO  •  database  •  HTTP  •  state mutation
```

**Pure functions are easier for both humans and agents to reuse.**

<br>

### 🔮 7. No Magic Values

| Replace | With |
|---|---|
| `42` | `MAX_LOGIN_ATTEMPTS` |
| `"/tmp"` | `TEMP_DIR` |
| `30` (timeout) | `REQUEST_TIMEOUT_SEC` |
| `"secret-key"` | env var |

```python
# Bad
if len(items) > 5: ...

# Good
MAX_BATCH_SIZE = 5
if len(items) > MAX_BATCH_SIZE: ...
```

<br>

### ⏳ 8. Temporary Code Must Expire

```python
# TEMP:
# reason: support old token format during migration
# remove-after: all mobile clients >= 4.2
```

> **Unmarked temporary code becomes permanent architecture.**

<br>

---

## Before Large Changes

Write a small **Architecture Manifest** before implementing.

```md
## Architecture Manifest

Goal:
  Add password reset without duplicating email or token logic.

Reuse:
  - EmailSender
  - TokenGenerator
  - UserRepository

Files:
  - auth/password_reset.py  : reset request and confirmation flow
  - auth/tokens.py          : extend token purpose enum
  - api/auth_routes.py      : HTTP boundary

Flow:
  request → validate email → create token → send → confirm → update

Verification:
  - unit tests for token purpose
  - flow test for reset confirmation
```

> The manifest should be **short**. It exists to prevent architecture drift *before* it starts.

<br>

---

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

<br>

---

## Install

```
Copy skill/skill.md into your agent skills directory,
or paste AGENTS.md into a repository root.
```

<br>

## Status

**Living protocol, not a framework.**

It should stay **small enough** to paste into an agent context  
and **concrete enough** to change real coding behavior.

<br>

---

<p align="center">
  <sub>⚕  Made for agents, by an agent  ·  Reuse first, create last</sub>
  <br>
  <img src="https://img.shields.io/badge/⚕-ai--native--coding--protocol-2C3E50?style=flat-square" alt="Protocol">
</p>
