# Changes in this fork

This is a fork of [google-labs-code/stitch-skills](https://github.com/google-labs-code/stitch-skills),
taken at upstream commit `4cf4db8` and reviewed on 18 November 2026 before use. The review found
nothing malicious: no hooks, no install scripts, no telemetry, no hidden text. Four things were
changed so the skills are safe to run with an agent that has shell access.

| Change | Where | Why |
|---|---|---|
| The Stitch API key is read from the `STITCH_API_KEY` environment variable | `upload-to-stitch` (skill and script), `manage-design-system` | Upstream told the agent to find the key by reading agent configuration files such as `~/.claude.json`, which hold the credentials of every connected server, and to pass it on the command line, where it reaches the shell history and the transcript |
| `allowed-tools` removed from every skill | all 16 `SKILL.md` files | Upstream granted unrestricted `Bash` to most skills. Without the block, the agent's normal permission prompts apply |
| Skill names use `stitch-` in place of `stitch::` | 8 skills, and every reference to them | `::` is not valid in an Agent Skills name (`^[a-z0-9-]+$`), so those skills failed validation and could not be uploaded to claude.ai |
| `python` in place of `python3` | commands in the skills, the script's first line | On Windows `python3` is the Microsoft Store alias stub |

Cross-links between skills in `code-to-design` name the sibling skill, because each skill is
installed as its own folder.

## Using it

- Install by copying the skill folders from this fork, not with `npx plugins add` or `npx skills add`,
  which fetch unreviewed tools and take upstream's copy.
- Stitch is a Google service. Prompts, design files and, in the code-to-design flow, a full HTML
  snapshot of the running application are sent to Google. Use it only for projects whose content
  may be sent there.
- Run `extract-static-html` only against your own local applications: it launches Chrome without
  its sandbox and fetches whatever the page references.
- Several skills run `npx` or `npm install` for third-party packages at their latest version. Keep
  the permission prompt on for those.
- `stitch-loop` is a self-continuing build loop. Run it supervised.

## Updating from upstream

```bash
git fetch upstream
git merge upstream/main
```

Then review what changed before installing, and check that no `allowed-tools` block, `stitch::`
name, `python3` call or config-file key lookup came back.
