---
name: almide-verify
description: Run type checking and tests on an Almide module against both Rust and TypeScript targets. Use after implementing or modifying an .almd module.
user-invocable: true
allowed-tools: Read, Bash, Grep
argument-hint: <module.almd>
---

# almide verify

Verify an Almide module compiles and passes tests on both targets.

## Input

`$ARGUMENTS` is the module `.almd` file path.

## Steps

1. Determine the test file path (same name with `_test` suffix).

2. Run verification:

```bash
# Type check
almide check $ARGUMENTS

# Test on Rust target
almide run <name>_test.almd

# Test on TypeScript target
almide run <name>_test.almd --target ts
```

3. Report results:

```
<name>: ✅ type-check  ✅ test-rust  ✅ test-ts
```

Or on failure:

```
<name>: ✅ type-check  ❌ test-rust
  Error: assertion failed at line 15
  Expected: ["a", "b", "c"]
  Got: ["a,b,c"]
```

4. If tests fail:
   - Consult `${CLAUDE_SKILL_DIR}/../references/troubleshooting.md` for common error patterns
   - Read the source and test files, identify the issue, and suggest a fix
   - Do NOT automatically edit — report the finding and let the user decide.
