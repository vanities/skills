# vanities/skills

Agent skills I use day-to-day. Straight from my `.claude` directory.

Built for [Claude Code](https://claude.com/claude-code) and any other agent runtime that follows the [agent skills](https://agentskills.io) convention (frontmatter + `SKILL.md`).

## Install

Symlink whichever ones you want into your `~/.claude/skills/` directory:

```bash
git clone git@github.com:vanities/skills.git ~/vanities-skills
ln -s ~/vanities-skills/skills/roast-me ~/.claude/skills/roast-me
```

Or use [skills.sh](https://skills.sh) when published there.

## Skills

### [`roast-me`](skills/roast-me)

Brutally honest critique mode. Role-play as a sharp, talented reviewer — recruiter, senior peer, principal engineer, founder — and tear into the work without diplomatic softening. Use when you actually want real feedback instead of a thumbs-up.

Triggers: "roast me", "be brutal", "what's actually wrong with this", asking for an honest critique.

## License

MIT — see [LICENSE](LICENSE).
