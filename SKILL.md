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

if cond then expr else expr                    // MUST have else
match subject { Pattern => expr, _ => expr }   // exhaustive
do { guard cond else ok(()) ... }              // loop (no while/loop keyword)
guard cond else err(msg)                       // early return
fn(x) => expr                                 // lambda
x |> f |> g                                   // pipe

let x = 1                                     // immutable
var y = 2                                     // mutable

type Name = { field: Type }                    // record
type Name = | Case1(T) | Case2                 // variant

[] = empty list    ok(v) / err(e) = Result    some(v) / none = Option
_ = match wildcard ONLY (not a variable name)
```

Built-in types: `Int`, `Float`, `String`, `Bool`, `Unit`, `Path`, `List[T]`, `Map[K,V]`, `Set[T]`, `Result[T,E]`, `Option[T]`

## When to Read References

- **Full syntax & stdlib API** → `${CLAUDE_SKILL_DIR}/references/cheatsheet.md`
- **Errors or unexpected behavior** → `${CLAUDE_SKILL_DIR}/references/troubleshooting.md`

Read references only when needed. For simple code edits, the inline rules above are sufficient.

## Key Constraints

- Use ONLY stdlib functions listed in the cheatsheet — do not invent APIs
- `[]` for generics, never `<>`
- No `print` — use `println`
- No `loop`/`while` — use `do { guard ... else }` pattern
- No exceptions — use `Result[T, E]`
- No null — use `Option[T]`
- No inheritance — use trait + impl
- Empty list = `[]` (no `list.new()`)
