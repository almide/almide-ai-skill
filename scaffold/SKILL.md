---
name: almide-scaffold
description: Generate an Almide module skeleton with type signatures and test stubs. Use when the user wants to create a new Almide module or stdlib package.
user-invocable: true
allowed-tools: Read, Write, Bash, Grep, Glob
argument-hint: <module-name> <description>
---

# almide scaffold

Generate a new Almide module skeleton from a name and description.

## Input

`$ARGUMENTS` should be: `<module-name> "<description>"` and optionally `--fn "sig1" --fn "sig2"` for function signatures.

Parse `$ARGUMENTS` to extract:
- `name`: the module name (first argument)
- `desc`: the description (quoted string)
- `fns`: any `--fn` flags with Almide function signatures

## Steps

1. Read `${CLAUDE_SKILL_DIR}/../references/cheatsheet.md` to understand Almide syntax conventions. If errors occur, consult `${CLAUDE_SKILL_DIR}/../references/troubleshooting.md`.

2. Generate `<name>.almd` with this structure:

```
// <name> — <description>
// Bundled stdlib module (written in Almide, auto multi-target)

fn <name>(args: Types) -> RetType = {
  // TODO: implement
  <zero-value>
}
```

Rules for stubs:
- Return type `String` → zero value `""`
- Return type `Int` → zero value `0`
- Return type `Bool` → zero value `false`
- Return type `List[T]` → zero value `[]`
- Return type `Map[K, V]` → zero value `map.new()`
- Return type `Option[T]` → zero value `none`
- Return type `Result[T, E]` → zero value `ok(<zero-value-of-T>)`
- Return type `Unit` → zero value `()`

3. Generate `<name>_test.almd` with test stubs:

```
import <name>

fn main() = {
  // <function-name>
  let result = <name>.<function>(example_input)
  assert(condition)

  println("<name>: all tests passed")
}
```

Generate at least one test per function. Use realistic example inputs.

4. Write both files to the current directory.

5. Report what was generated.

## If no `--fn` flags are provided

Infer reasonable function signatures from the module name and description. For example:
- `csv "CSV parser"` → `parse(input: String) -> List[List[String]]`, `stringify(rows: List[List[String]]) -> String`
- `semver "Semantic versioning"` → `parse(v: String) -> Result[Map[String, Int], String]`, `compare(a: String, b: String) -> Int`

State the inferred signatures and ask the user to confirm before generating.
