# Almide Troubleshooting

## Compiler Errors

### "expected `else` branch"
`if` without `else` is a syntax error. Every `if` must have an `else`.
```
// BAD
if x > 0 then println("yes")

// GOOD
if x > 0 then println("yes") else ()
// Or use guard:
guard x > 0 else err("not positive")
```

### "unknown function" / "unresolved import"
Only stdlib functions listed in `references/cheatsheet.md` exist. Do NOT invent APIs.
Common mistakes:
- `list.new()` → use `[]`
- `list.empty()` → use `[]`
- `print(x)` → use `println(x)`
- `string.replace(s, a, b)` → not available, use split+join pattern
- `list.push(xs, x)` → use `xs ++ [x]`
- `map.get(m, k)` → not available; pattern match or use fold

### "_ cannot be used as variable name"
`_` is ONLY valid in match patterns. `let _ = x` is a syntax error.
```
// BAD
let _ = some_call()

// GOOD — just call it without binding
some_call()
// Or bind to a named variable
let _unused = some_call()
```

### "type mismatch: expected Result, got T"
Functions marked `effect fn` must return `Result[T, E]`. Wrap the return value:
```
// BAD
effect fn read(path: Path) -> Result[String, IoError] = "hello"

// GOOD
effect fn read(path: Path) -> Result[String, IoError] = ok("hello")
```

### "cannot use `<` as generic parameter"
Almide uses `[]` for generics, NOT `<>`. `<` and `>` are always comparison operators.
```
// BAD
type Pair<A, B> = { first: A, second: B }

// GOOD
type Pair[A, B] = { first: A, second: B }
```

### "loop is not a keyword"
There is no `loop` or `while`. Use `do { ... }` with `guard ... else` for loops.
```
// BAD
while count > 0 { ... }

// GOOD
do {
  guard count > 0 else ok(())
  // loop body
  count = count - 1
}
```

## Runtime Errors

### Test assertion failed
1. Read the error message — it shows expected vs actual values
2. Check the implementation, NOT the test signatures
3. Common causes:
   - Off-by-one in `string.slice` (0-based, end-exclusive)
   - `list.get` returns `Option[T]`, not `T` — must unwrap with match
   - Missing edge case (empty input, single element)

### "almide: command not found"
The `almide` CLI is not installed or not in PATH.
```bash
# Check if available
which almide

# If using nix
nix develop
```

## Cross-Target Issues

### Passes on Rust but fails on TypeScript (or vice versa)
- Avoid relying on integer overflow behavior (differs between targets)
- String encoding edge cases may differ
- Floating point precision may vary — use epsilon comparison for Float
- Both targets must pass before a module is considered complete

## Build Strategy

### When to re-read the cheatsheet
- Writing a new module from scratch → read full cheatsheet
- Fixing a small bug → usually not needed
- Using an unfamiliar stdlib function → check the specific section

### Maximum retry attempts
- `almide-fill-module` and `almide-proliferate`: max 3 fix attempts
- After 3 failures, report remaining errors to the user
- Do NOT change test signatures or function types to make tests pass
