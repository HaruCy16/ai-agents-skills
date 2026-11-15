# AI Agent Skills

A collection of Claude Code skills for full-stack web developers. Each folder contains a `SKILL.md` that Claude loads when the task matches its description.

## Skills

| Area | Skill |
|---|---|
| Frontend | `frontend-architecture`, `web-design`, `accessibility`, `web-performance` |
| Backend | `api-design`, `database-design`, `auth-and-sessions` |
| Security | `web-security`, `dependency-security` |
| Quality & delivery | `testing-web`, `code-review-web`, `deployment-cicd`, `seo-and-metadata` |

## Install

Global (all projects):

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cp -r <repo-name>/*/ ~/.claude/skills/
```

Single project:

```bash
mkdir -p .claude/skills
cp -r <repo-name>/web-security .claude/skills/
```

Restart Claude Code, then ask for something that matches a skill (for example, "review this login route for security issues").

## Add a skill

1. Create `skill-name/SKILL.md` with `name` and `description` frontmatter.
2. Make the description say what the skill does and when to use it.
3. Keep `SKILL.md` under ~500 lines; move long material into `references/`.
4. Add it to the table above.
