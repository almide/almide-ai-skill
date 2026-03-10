---
name: almide
description: Write code in Almide, a programming language designed for LLM code generation. Use when the user asks to write .almd files, Almide code, or mentions the Almide language.
---

# Almide Quick Rules

File extension: `.almd`. Generics use `[]` not `<>`. No semicolons (newline = separator).

## Essential Syntax (inline — no file read needed for simple tasks)

```
fn name(x: Type) -> RetType = expr            // pure function
effect fn name(x: Type) -> Result[T, E] = expr // side effects
async fn name(x: Type) -> Result[T, E] = expr  // async
mod fn / local fn                              // visibility (default: public)

if cond then expr else expr                    // MUST have else
match subject { Pattern => expr, _ => expr }   // exhaustive
for x in xs { ... }                            // iteration (preferred)
for i in 0..n { ... }                          // range loop (0..n exclusive, 0..=n inclusive)
do { guard cond else ok(()) ... }              // dynamic-condition loop
guard cond else err(msg)                       // early return
fn(x) => expr                                 // lambda
x |> f |> g                                   // pipe
"hello ${name}"                                // string interpolation
"""multi-line heredoc"""                        // heredoc (strips indent)

let x = 1                                     // immutable
var y = 2                                     // mutable

type Name = { field: Type }                    // record
type Name = | Case1(T) | Case2                 // variant

[] = empty list    map.new() = empty map    ++ = concat (list & string)
ok(v) / err(e) = Result    some(v) / none = Option
_ = match wildcard ONLY (not a variable name)
```

Built-in types: `Int`, `Float`, `String`, `Bool`, `Unit`, `Path`, `List[T]`, `Map[K,V]`, `Set[T]`, `Result[T,E]`, `Option[T]`

Auto-imported modules: `string`, `list`, `map`, `int`, `float`, `fs`, `path`, `env`, `process`, `io`

## When to Read References

- **Full syntax & stdlib API** → `${CLAUDE_SKILL_DIR}/references/cheatsheet.md`
- **Errors or unexpected behavior** → `${CLAUDE_SKILL_DIR}/references/troubleshooting.md`

Read references only when needed. For simple code edits, the inline rules above are sufficient.

## Key Constraints

- Use ONLY stdlib functions listed in the cheatsheet — do not invent APIs
- `[]` for generics, never `<>`
- No `print` — use `println` (or `io.print` for no-newline)
- Use `for...in` for iteration, NOT `do { var i = 0; guard ... }`
- No exceptions — use `Result[T, E]`
- No null — use `Option[T]`
- No inheritance — use trait + impl
- Empty list = `[]`, empty map = `map.new()`
- `println(int.to_string(x))` — no implicit conversion to String
- User-defined generic functions are NOT supported — use concrete types
