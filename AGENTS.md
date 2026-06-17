# Guidance for AI Agents Working in This Repo

This repository contains ALGOPACK skills for AI coding agents. When editing or
adding skills, follow these rules.

## Repo structure

- Runtime skills live under `skills/`.
- Each direct child of `skills/` is one skill.
- Each skill directory must contain `SKILL.md`.
- The skill directory name must exactly match the `name` in that skill's
  frontmatter, for example `skills/algopack-market-data/` and
  `name: algopack-market-data`.
- Keep long supporting material in the skill's `references/` directory.
- `docs/` contains maintainer source material and is not part of the skill
  runtime surface.

## Skill families

- `algopack-*` skills cover direct REST/ISS workflows for users working through
  an LLM: browser URLs, curl, raw JSON/CSV/HTML output, ISS `columns` + `data`
  normalization, and `start` pagination.
- `moexalgo-*` skills cover Python `moexalgo` library workflows:
  `Market`/`Ticker` helpers, DataFrame-first examples, `.env` token setup,
  library parameters such as `offset`, and library-specific caveats.
- `moex-iss` covers generic ISS and ISS+ mechanics that are not specific to one
  ALGOPACK dataset: discovery, calendars, boardgroups, response formats,
  pagination, and websocket/STOMP concepts.
- Keep intent split. Do not put Python library quick starts in direct REST
  skills, and do not use raw REST pagination parameters as `moexalgo` library
  examples unless explaining a direct REST fallback.

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
- Tell users plainly when real-time, fully up-to-date, or subscriber-only data
  requires a valid token and product entitlement; public ISS data can be delayed
  or limited.
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

- Run `find skills -maxdepth 2 -name SKILL.md -print | sort` to verify skill
  locations.
- Run `rg -n "^name:|^description:" skills/*/SKILL.md` to inspect skill metadata.
- Confirm each `references/` path mentioned from `SKILL.md` exists.

## References

- Agent Skills specification: https://agentskills.io/specification
- Codex Agent Skills: https://developers.openai.com/codex/skills
