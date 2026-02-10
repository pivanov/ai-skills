# AI Skills

Reusable skills for AI coding agents (Claude Code, Cursor, etc.)

## Installation

```bash
# Install a specific skill
npx skills add pivanov/ai-skills --skill split-tasks

# List available skills in this repo
npx skills add pivanov/ai-skills --list
```

## Available Skills

| Skill | Description | Version |
|-------|-------------|---------|
| [split-tasks](./skills/split-tasks) | Split 3+ independent tasks across parallel agents | 1.0.0 |

## Adding New Skills

1. Create a new directory: `mkdir skills/my-skill`
2. Add `skills/my-skill/SKILL.md` with frontmatter:

   ```yaml
   ---
   name: my-skill
   description: "What it does. Triggers: keyword1, keyword2"
   ---
   ```

3. Update this README and CHANGELOG
4. Tag release: `git tag v1.x.0 && git push --tags`

## License

MIT
