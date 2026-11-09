# AX-AI-Plugins

The coding-CLI plugins (skills) the AX AI platform installs for its agents. One folder per plugin; the platform reads
each one through `aatmix/config/plugins.yaml` (`url` + `ref` + `paths`).

| Folder | What it is | Source |
|---|---|---|
| `ts-full-stack/` | TypeScript/JS full-stack house plugin: architecture, style, review and the develop-and-test skill set | Ours (history kept from `chandra-pangea/plugin-ax-ai-ts-full-stack`) |
| `figma-design/` | Figma design-system skills, from the branding guideline to production screens | Ours (history kept from `chandra-pangea/plugin-ax-ai-figma-design`) |
| `python-skills/` | Python best-practices skills | Copy of `fcakyon/claude-codex-settings` `v2.8.0` (`plugins/python-skills`), Apache-2.0 |
| `react-skills/` | React and React Native best-practices skills | Copy of `fcakyon/claude-codex-settings` `v2.8.0` (`plugins/react-skills`), Apache-2.0 |

## Layout

- `ts-full-stack/` and `figma-design/` hold one folder per CLI: `claude/` (with `.claude-plugin/`) and `codex/` (with `.codex-plugin/`).
- `python-skills/` and `react-skills/` are one folder for both CLIs.

## Rules the platform enforces

A plugin may not declare hooks or MCP servers and may not contain symbolic links.

## Third-party plugins

`python-skills/` and `react-skills/` are unmodified copies taken at commit `60b1a92e585f15236331e10c763bc6d3913a005b`
(tag `v2.8.0`) of https://github.com/fcakyon/claude-codex-settings, licensed under Apache-2.0. The licence text is in
each folder's `LICENSE`. To take a newer upstream version, replace the folder with the upstream one and update this note.
