---
name: almide-mine-prior-art
description: Discover reference implementations of a module in other languages, filter by license, extract test aspects, and generate comprehensive Almide tests. Use when creating a new module that has equivalents in Python/Ruby/Go/Rust/JS.
user-invocable: true
allowed-tools: Read, Write, Bash, WebSearch, WebFetch, Grep, Glob
argument-hint: <module-name>
---

# almide mine-prior-art

Given a module name, find battle-tested implementations in other languages, extract their test coverage patterns, and generate comprehensive Almide tests.

## Input

`$ARGUMENTS` is the module name (e.g., `csv`, `toml`, `semver`).

Also read any existing `<name>.almd` stub in the current directory to understand the target API surface.

## Steps

### 1. Reference Discovery

Search for well-known implementations of `$ARGUMENTS` across languages:

| Language | Where to look |
|----------|---------------|
| Python | stdlib or PyPI (e.g., `csv` stdlib, `tomllib`) |
| Ruby | stdlib or RubyGems |
| Go | stdlib `encoding/csv`, or popular packages |
| Rust | crates.io (e.g., `csv`, `toml`, `semver`) |
| JavaScript | npm (e.g., `papaparse`, `csv-parse`) |

Use WebSearch to find the top 3-5 implementations. For each, note:
- Repository URL
- License
- Approximate GitHub stars (popularity signal)

### 2. License Filter

**Keep only** libraries with these licenses:
- MIT, Apache-2.0, BSD-2-Clause, BSD-3-Clause, ISC, Unlicense, CC0, public domain, PSF (Python)

**Reject** (do not read test files from these):
- GPL, LGPL, AGPL, MPL, SSPL, proprietary, unknown

Record the license for each accepted reference.

### 3. Test Case Analysis

For each accepted reference:
- Find test files (WebSearch or WebFetch the repo's test directory)
- Read the test file contents
- Extract **test aspects** (what behavior each test validates), NOT code

Output a list like:
```
Reference: Python csv (PSF)
Aspects:
- basic comma-separated parsing
- quoted fields containing commas
- escaped quotes (doubled "")
- newlines within quoted fields
- empty fields
- custom delimiter
- ...
```

### 4. Aspect Synthesis

Merge aspects from all references into a unified, deduplicated list.
Group by category:

```
## Test Aspects for: csv

### Parsing — basics
- [ ] comma-separated values
- [ ] multiple rows
- [ ] single row, single column

### Parsing — quoting
- [ ] quoted fields
- [ ] quotes containing commas
- [ ] escaped quotes (doubled "")
- [ ] newlines within quotes

### Parsing — edge cases
- [ ] empty input → empty list
- [ ] trailing newline
- [ ] empty fields (a,,b)
- [ ] whitespace handling

### Stringify
- [ ] basic round-trip
- [ ] fields requiring quoting
- [ ] stringify ∘ parse ≈ identity

### Options
- [ ] custom delimiter (tab, semicolon)
- [ ] header row extraction
```

### 5. Generate Almide Tests

Read `${CLAUDE_SKILL_DIR}/../references/cheatsheet.md` for Almide syntax.

Translate every aspect into a concrete test assertion in `<name>_test.almd`:

```almide
import <name>

fn main() = {
  // --- Parsing basics ---
  let rows = csv.parse("a,b,c\n1,2,3")
  assert_eq(list.len(rows), 2)
  assert_eq(list.get(rows, 0), some(["a", "b", "c"]))

  // --- Quoting ---
  let quoted = csv.parse("\"hello, world\",b\n")
  assert_eq(list.get(list.get(quoted, 0), some(0)), some("hello, world"))

  // ... one assertion block per aspect ...

  println("<name>: all tests passed")
}
```

Rules:
- Do NOT copy any source code from references
- Tests must be independently written in Almide syntax
- Use concrete input/output values, not vague descriptions
- Cover every aspect from the synthesis

### 6. Write Attribution Header

Add a comment at the top of the test file:

```almide
// Test aspects derived from: <list of references with licenses>
// No source code was copied. Tests independently written based on
// observed behavioral patterns across reference implementations.
```

### 7. Output

Write `<name>_test.almd` to the current directory. Report:
- Number of references analyzed
- Number of test aspects extracted
- Number of test assertions generated
