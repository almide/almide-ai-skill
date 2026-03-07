# Selmite AI Skill

AI-readable language reference for [Selmite](https://github.com/selmite/selmite), a programming language designed for LLM code generation.

## What is this?

This repository contains a Claude Code skill and `CHEATSHEET.md` — a ~340-line quick reference that gives an LLM everything it needs to write valid Selmite code, with zero prior knowledge of the language.

## Setup

### Claude Code

Clone this repo into your project's skill directory:

```bash
# Per-project
mkdir -p .claude/skills
git clone https://github.com/selmite/selmite-ai-skill.git .claude/skills/selmite

# Or globally (available in all projects)
git clone https://github.com/selmite/selmite-ai-skill.git ~/.claude/skills/selmite
```

Then invoke with `/selmite` in Claude Code, or Claude will automatically use it when working with `.selm` files.

### Other AI Tools

Copy the contents of `CHEATSHEET.md` into your system prompt or context window.

## Benchmark

An LLM given only this cheatsheet (no other context) achieved **100% pass rate** (10/10 trials, 11/11 tests each) on the [MiniGit benchmark](https://github.com/mame/ai-coding-lang-bench).

## License

MIT
