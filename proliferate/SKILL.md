---
name: almide-proliferate
description: Mass-produce Almide modules by running the full pipeline (scaffold → mine prior art → fill → verify) for one or more modules. Use when creating multiple packages at once.
user-invocable: true
allowed-tools: Read, Write, Edit, Bash, WebSearch, WebFetch, Grep, Glob
argument-hint: <spec.toml or module-name>
---

# almide proliferate

Run the full module generation pipeline: scaffold → mine prior art → fill → verify.

## Input

`$ARGUMENTS` is either:
- A path to a `spec.toml` file defining multiple modules
- A single module name (e.g., `csv`)

### spec.toml Format

```toml
[[module]]
name = "csv"
description = "CSV parser and writer"
fns = [
  "parse(input: String) -> List[List[String]]",
  "stringify(rows: List[List[String]]) -> String",
]

[[module]]
name = "ini"
description = "INI file parser"
fns = [
  "parse(input: String) -> Map[String, Map[String, String]]",
  "stringify(sections: Map[String, Map[String, String]]) -> String",
]
```

## Pipeline Per Module

For each module in the spec, execute these steps in order:

### Step 1: Scaffold

Read `${CLAUDE_SKILL_DIR}/../references/cheatsheet.md` for Almide syntax.

Generate `<name>.almd` with typed stubs and zero-value returns. Use the function signatures from spec.toml. If no signatures are specified, infer from the module name and description.

### Step 2: Mine Prior Art

Search for reference implementations in Python, Ruby, Go, Rust, and JavaScript:
- Filter by license (MIT/Apache/BSD/ISC/Unlicense/PSF only)
- Extract test aspects from accepted references
- Synthesize a unified coverage matrix
- Generate `<name>_test.almd` with comprehensive test assertions

Add attribution header listing references and licenses.

### Step 3: Fill

Implement all function bodies to pass the generated tests:
- Use only stdlib functions from the cheatsheet
- Keep implementations simple and readable
- Preserve exact function signatures

### Step 4: Verify

```bash
almide check <name>.almd
almide run <name>_test.almd
almide run <name>_test.almd --target ts
```

Both Rust and TypeScript targets must pass.

### Step 5: Retry (if needed)

If verification fails:
- Read the error message
- Fix the implementation
- Re-verify
- Maximum 3 attempts per module

### Step 6: Report

After each module, output a status line:

```
csv:    ✅ scaffold  ✅ prior-art (5 refs)  ✅ fill  ✅ rust  ✅ ts
ini:    ✅ scaffold  ✅ prior-art (3 refs)  ✅ fill  ✅ rust  ❌ ts (1 failure)
        → retry 1: ✅ rust ✅ ts
semver: ✅ scaffold  ✅ prior-art (4 refs)  ✅ fill  ✅ rust  ✅ ts
```

## Output Structure

For each module, produce:
```
<name>.almd          # Implemented module
<name>_test.almd     # Comprehensive tests with attribution
```

## After All Modules

Print a summary:
```
Proliferation complete: 3/3 modules generated
- csv:    5 functions, 24 test assertions, both targets pass
- ini:    2 functions, 15 test assertions, both targets pass
- semver: 3 functions, 19 test assertions, both targets pass
```
