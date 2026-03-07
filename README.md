# Almide AI Skill

AI-readable language reference for [Almide](https://github.com/almide/almide), a programming language designed for LLM code generation.

## What is this?

This repository contains a Claude Code skill and `CHEATSHEET.md` — a ~340-line quick reference that gives an LLM everything it needs to write valid Almide code, with zero prior knowledge of the language.

## Setup

### Claude Code

Clone this repo into your project's skill directory:

```bash
# Per-project
mkdir -p .claude/skills
git clone https://github.com/almide/almide-ai-skill.git .claude/skills/almide

# Or globally (available in all projects)
git clone https://github.com/almide/almide-ai-skill.git ~/.claude/skills/almide
```

Then invoke with `/almide` in Claude Code, or Claude will automatically use it when working with `.almd` files.

### Other AI Tools

Copy the contents of `CHEATSHEET.md` into your system prompt or context window.

## Benchmark

An LLM given only this cheatsheet (no other context) achieved **100% pass rate** (10/10 trials, 11/11 tests each) on the [MiniGit benchmark](https://github.com/mame/ai-coding-lang-bench).

## License

MIT
