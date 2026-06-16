# Guidance for AI Agents Working in This Repo

This repository contains ALGOPACK skills for AI coding agents. When editing or
adding skills, follow these rules.

## Repo structure

- Each top-level `algopack-*` directory is one skill.
- Each skill directory must contain `SKILL.md`.
- The skill directory name must exactly match the `name` in that skill's
  frontmatter, for example `algopack-market-data/` and
  `name: algopack-market-data`.
- Keep long supporting material in the skill's `references/` directory.
- `docs/` contains maintainer source material and is not part of the skill
  runtime surface.

## SKILL.md requirements

- Frontmatter:
  - `name` is required. Use lowercase words separated by hyphens, and match the
    parent directory name.
  - `description` is required. Describe what the skill does and when to use it.
    Include concrete trigger terms users naturally ask for, such as product
    names, method names, endpoint families, fields, and error codes.
- Body:
  - Start with an H1 matching the skill topic.
  - Include `Overview`, `Quick Start` or `Core Workflow`, `References`, and
    `Boundary` sections where they fit the skill.
  - Prefer concise, actionable instructions over copied documentation.
  - Put lengthy field lists, endpoint tables, and interpretation notes in
    `references/`.

## Content conventions

- Prefer DataFrame-first `moexalgo` examples for supported workflows.
- Use direct ISS or REST examples only when the skill scope is raw endpoints,
  calendar/discovery work, unsupported APIs, or non-Python integration.
- Use `APIKEY` in examples and never include real credentials.
- Keep examples copy-pasteable and minimal. Import `session`, `Market`, and
  `Ticker` only when used.
- State access or subscription requirements when a workflow may fail with
  `401`, `403`, `429`, or missing data.
- Do not imply that ALGOPACK places orders. These skills cover data and
  analytics workflows.

## Maintenance checklist

- When adding a skill, create `<skill-name>/SKILL.md` and optional
  `<skill-name>/references/*.md`.
- If README.md lists skills or structure, update it when adding, removing, or
  renaming a skill.
- If moving or renaming a skill, update the folder name, frontmatter, reference
  paths, and install instructions together.
- Do not add Codex-app-only `agents/openai.yaml` metadata. Keep this collection
  compatible with broader agent-skill tooling.
- Keep `CLAUDE.md` as a symbolic link to `AGENTS.md`.

## Validation

- Run `find . -maxdepth 2 -name SKILL.md -print | sort` to verify skill
  locations.
- Run `rg -n "^name:|^description:" */SKILL.md` to inspect skill metadata.
- Confirm each `references/` path mentioned from `SKILL.md` exists.

## References

- Agent Skills specification: https://agentskills.io/specification
- Codex Agent Skills: https://developers.openai.com/codex/skills
