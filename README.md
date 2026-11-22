# claude-skills

[Claude Code skills](https://code.claude.com/docs/en/skills) I've written and use. Each one is
packaged as its own plugin, so you can install only the ones you want.

| Skill | What it does |
| :- | :- |
| [agent-readiness](plugins/agent-readiness) | Runs the [isitagentready.com](https://isitagentready.com) scan on a website, sorts out which checks apply, and implements the ones that matter: robots.txt Content Signals, Markdown twins with `Accept` negotiation, and `Link` alternate headers. |
| [card-rules-rewrite](plugins/card-rules-rewrite) | Rewrites card game rules and strategy pages so they read like a knowledgeable player wrote them and open with a definition that works as a search snippet. |
| [push-to-confluence](plugins/push-to-confluence) | Pushes a local Markdown file to a Confluence page through the Atlassian MCP server. Rewrites `#anchor` links into full Confluence URLs (with Confluence's capitalized anchor IDs) on the way up, and leaves the local file untouched. |
| [remove-ai-tells](plugins/remove-ai-tells) | Rewrites prose (docs, READMEs, articles, emails) so it reads as human-written, cuts padding, and drops claims nobody checked. |

## Install

Add this repo as a plugin marketplace, then install the skills you want:

```bash
claude plugin marketplace add jimjonah/claude-skills
claude plugin install agent-readiness@jimjonah-skills
```

Or inside a session: `/plugin marketplace add jimjonah/claude-skills`, then pick from `/plugin`.
`claude plugin marketplace update jimjonah-skills` pulls new versions.

Claude loads a skill on its own when the task matches its description. To run one directly, use
`/<plugin>:<skill>`, for example `/agent-readiness:agent-readiness https://example.com`.

### Without the plugin system

Copy a skill folder into `~/.claude/skills/` (every project) or `.claude/skills/` (one project):

```bash
git clone https://github.com/jimjonah/claude-skills.git
cp -r claude-skills/plugins/remove-ai-tells/skills/remove-ai-tells ~/.claude/skills/
```

## Adding a skill

1. Create `plugins/<name>/skills/<name>/SKILL.md`.
2. Add `plugins/<name>/.claude-plugin/plugin.json` with the same `name`.
3. Add an entry to `.claude-plugin/marketplace.json` and a row to the table above.
4. Run `claude plugin validate .`

Bump a plugin's `version` in its `plugin.json` when you change it, so people who installed it get
the update.

## License

MIT. See [LICENSE](LICENSE).
