# Selmite AI Skill

AI-readable language reference for [Selmite](https://github.com/selmite/selmite), a programming language designed for LLM code generation.

## What is this?

This repository contains `CHEATSHEET.md` — a ~340-line quick reference that gives an LLM everything it needs to write valid Selmite code, with zero prior knowledge of the language.

## Usage

Feed `CHEATSHEET.md` as context to your LLM when generating Selmite code.

### Claude Code

Add to your project's `.claude/settings.json`:

```json
{
  "skills": ["selmite/selmite-ai-skill"]
}
```

### Other AI Tools

Copy the contents of `CHEATSHEET.md` into your system prompt or context window.

## Benchmark

An LLM given only this cheatsheet (no other context) achieved **100% pass rate** (10/10 trials, 11/11 tests each) on the [MiniGit benchmark](https://github.com/mame/ai-coding-lang-bench).

## License

MIT
