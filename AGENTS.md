# Agent Rules

Use the AI-Native Coding Protocol for this repository.

## Core Rule

Before writing new code:

1. Search for existing behavior.
2. Reuse existing code when possible.
3. Extend existing code before creating new code.
4. Avoid duplicate logic.
5. Keep modules domain-coherent.
6. Make side effects explicit.
7. Add intent metadata to public APIs and important modules.
8. Verify changes with tests or explain why verification was not possible.

## Large Changes

Before implementing a large feature or refactor, write a short architecture manifest:

- files to change
- responsibility of each file
- reused code
- new code, if needed
- main flow
- verification plan

## Temporary Code

Temporary code must include:

```text
TEMP:
reason:
remove-after:
```
