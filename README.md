# AI Skills

Reusable skills for AI coding agents (Claude Code, Cursor, etc.)

## Installation

```bash
# Install a specific skill
npx skills add pivanov/ai-skills@split-tasks

# Install all skills
npx skills add pivanov/ai-skills --all

# Install for specific agents
npx skills add pivanov/ai-skills@split-tasks --agent claude-code cursor
```

## Available Skills

| Skill | Description | Version |
|-------|-------------|---------|
| [split-tasks](./split-tasks) | Split 3+ independent tasks across parallel agents | 1.0.0 |

## Adding New Skills

1. Create a new directory: `mkdir my-skill`
2. Add `SKILL.md` with frontmatter:
   ```yaml
   ---
   name: my-skill
   version: 1.0.0
   description: What it does. Triggers: "keyword1", "keyword2".
   ---
   ```
3. Update this README and CHANGELOG
4. Tag release: `git tag v1.x.0 && git push --tags`

## License

MIT
