# vanities/skills

Agent skills and rules I use day-to-day.

Built for any agent runtime that follows the [agent skills](https://agentskills.io) convention (frontmatter + `SKILL.md`).

## Install

Clone and symlink the ones you want into your agent's skills directory:

```bash
git clone git@github.com:vanities/skills.git ~/vanities-skills
ln -s ~/vanities-skills/skills/roast-me <your-agent-skills-dir>/roast-me
```

Or use [skills.sh](https://skills.sh) when published there.

## Skills

### [`roast-me`](skills/roast-me)

Brutally honest critique mode. Role-play as a sharp, talented reviewer — recruiter, senior peer, principal engineer, founder — and tear into the work without diplomatic softening. Use when you actually want real feedback instead of a thumbs-up.

Triggers: "roast me", "be brutal", "what's actually wrong with this", asking for an honest critique.

### [`instrument`](skills/instrument)

Deliberately retrofit logging and timing into existing code — a systematic observability sweep over a file, module, or function. The on-demand companion to the [`log-everything`](#log-everything) rule: the rule covers code you're already editing, this skill lights up code you're not.

Triggers: "instrument this", "add logging", "add timing", "make this observable", debugging something opaque.

## Rules

Always-on instruction files — *not* invoked like skills. They're meant to be auto-loaded into every session by agents that read a global rules directory (symlink into `~/.claude/rules/` for Claude Code) or referenced from a global instruction file (e.g. Hermes `SOUL.md`).

### [`log-everything`](rules/log-everything.md)

Bias toward observable code: log freely at boundaries and decisions, time operations as lightweight spans, use log levels so verbosity is a dial, prefix lines with scope tags, and never log secrets or PII. Install:

```bash
ln -s ~/vanities-skills/rules/log-everything.md ~/.claude/rules/log-everything.md
```

## License

GNU General Public License v3.0 — see [LICENSE](LICENSE).
