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
- `List.new()` → use `[]`
- `print(x)` → use `println(x)`
- `list.push(xs, x)` → use `xs ++ [x]`
- `string.length(s)` → use `string.len(s)`
- `list[1, 2, 3]` → use `[1, 2, 3]` (list is a module, not a constructor)
- `each(xs, f)` → use `list.each(xs, f)` (all stdlib fns need module prefix)
- `1 :: 2 :: []` → use `[1, 2]` (no cons operator)

Auto-imported modules (no `import` needed): `string`, `list`, `map`, `int`, `float`, `fs`, `path`, `env`, `process`, `io`
Requires `import`: `json`, `math`, `random`, `regex`, `time`, `encoding`, `args`

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
Use `for...in` for iteration or `do { ... }` with `guard` for dynamic conditions.
```
// BAD
while count > 0 { ... }
for (let i = 0; i < n; i++) { ... }

// GOOD — iterate over a list
for item in items {
  println(item)
}

// GOOD — iterate with index using range
for i in 0..n {
  let item = list.get_or(items, i, "")
  println(item)
}

// GOOD — dynamic break condition (linked-list, streaming, etc.)
do {
  guard count > 0 else ok(())
  count = count - 1
}
```

### "implicit conversion" / printing non-String values
Almide has no implicit conversion. Convert explicitly:
```
// BAD
println(42)
println(x)  // where x is Int

// GOOD
println(int.to_string(42))
println(int.to_string(x))
println(float.to_string(3.14))
```

### "generic functions not supported"
User-defined generic functions are NOT supported. Use concrete types.
```
// BAD
fn identity[T](x: T) -> T = x

// GOOD — use concrete types
fn identity_int(x: Int) -> Int = x
fn identity_string(x: String) -> String = x
```

## Runtime Errors

### Test assertion failed
1. Read the error message — it shows expected vs actual values
2. Check the implementation, NOT the test signatures
3. Common causes:
   - Off-by-one in `string.slice` (0-based, end-exclusive)
   - `list.get` returns `Option[T]`, not `T` — use `list.get_or` or match
   - Missing edge case (empty input, single element)
   - Forgot `int.to_string()` before string concatenation

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
