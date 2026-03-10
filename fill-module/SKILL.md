---
name: almide-fill-module
description: Implement an Almide module stub by filling in function bodies to pass all tests. Use when you have a .almd stub with TODOs and a corresponding test file.
user-invocable: true
allowed-tools: Read, Write, Edit, Bash, Grep, Glob
argument-hint: <module.almd>
---

# almide fill-module

Given a stub `.almd` file (with `// TODO: implement` bodies) and a test file, implement all functions to pass the tests.

## Input

`$ARGUMENTS` is the path to the stub `.almd` file (e.g., `csv.almd`).

## Steps

### 1. Read Context

- Read the stub file (`$ARGUMENTS`)
- Read the corresponding test file (same name with `_test` suffix)
- Read `${CLAUDE_SKILL_DIR}/../references/cheatsheet.md` for Almide syntax reference
- If the stub imports other modules, check what stdlib functions are available in the cheatsheet

### 2. Analyze Requirements

From the test file, extract:
- What each function should return for given inputs
- Edge cases being tested
- Expected error conditions

From the stub file, extract:
- Function signatures (types are already locked)
- Module imports available

### 3. Implement

Replace each `// TODO: implement` body with a working implementation.

Rules:
- Preserve the exact function signatures — do NOT change types
- Use only stdlib functions listed in the cheatsheet
- Use only Almide syntax from the cheatsheet — do not invent APIs
- Prefer simple, readable implementations over clever ones
- Use `match` for branching, `do { guard ... }` for loops
- Use `list.fold`, `list.map`, `list.filter` for collection processing
- String processing: use `string.split`, `string.trim`, `string.slice`, `string.contains`

### 4. Verify

Run the following commands to check the implementation:

```bash
# Type check
almide check <module>.almd

# Run tests (Rust target)
almide run <module>_test.almd

# Run tests (TypeScript target)
almide run <module>_test.almd --target ts
```

Both targets must pass.

### 5. Fix Errors

If type check or tests fail:
- Consult `${CLAUDE_SKILL_DIR}/../references/troubleshooting.md` for common error patterns
- Read the error message carefully
- Fix the implementation (not the tests or signatures)
- Re-run verification
- Maximum 3 fix attempts. If still failing after 3, report the remaining errors to the user.

### 6. Report

Output:
- Functions implemented (count)
- Test results (pass/fail per target)
- Any notes about implementation choices
