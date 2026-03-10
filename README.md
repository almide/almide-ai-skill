# Almide AI Skills

AI-readable language reference and module proliferation toolkit for [Almide](https://github.com/almide/almide), a programming language designed for LLM code generation.

## Skills

| Skill | Command | What it does |
|-------|---------|-------------|
| **almide** | `/almide` | Language reference — gives the LLM everything it needs to write valid Almide code |
| **scaffold** | `/almide-scaffold csv "CSV parser"` | Generate a module skeleton with typed stubs and test stubs |
| **mine-prior-art** | `/almide-mine-prior-art csv` | Find reference implementations in other languages, extract test aspects, generate comprehensive tests |
| **fill-module** | `/almide-fill-module csv.almd` | Implement a stub module to pass all tests |
| **verify** | `/almide-verify csv.almd` | Type-check and test a module on both Rust and TypeScript targets |
| **proliferate** | `/almide-proliferate spec.toml` | Run the full pipeline (scaffold → mine → fill → verify) for multiple modules |

### Module Generation Pipeline

```
/almide-scaffold csv "CSV parser"
        │
        ▼  csv.almd (stubs)
/almide-mine-prior-art csv
        │
        ▼  csv_test.almd (comprehensive tests from 5 languages)
/almide-fill-module csv.almd
        │
        ▼  csv.almd (implemented)
/almide-verify csv.almd
        │
        ▼  ✅ type-check  ✅ test-rust  ✅ test-ts
```

Or run the whole pipeline at once:

```
/almide-proliferate spec.toml
```

## Setup

### Claude Code

```bash
# Per-project
mkdir -p .claude/skills
git clone https://github.com/almide/almide-ai-skill.git .claude/skills/almide

# Or globally (available in all projects)
git clone https://github.com/almide/almide-ai-skill.git ~/.claude/skills/almide
```

All skills are auto-discovered by Claude Code from the nested directory structure.

### Other AI Tools

Copy the contents of `references/cheatsheet.md` into your system prompt or context window.

## Benchmark

An LLM given only the cheatsheet (no other context) achieved **100% pass rate** (10/10 trials, 11/11 tests each) on the [MiniGit benchmark](https://github.com/mame/ai-coding-lang-bench).

## License

MIT
