# AGENTS.md — pod-versatiles

Standalone candy repo for the `versatiles` candy — the VersaTiles CLI
(versatiles-rs) and a tile server serving vector tiles over HTTP on port 8090.
The candy lives in `charly.yml` at the repo root.

Canonical files:

- `charly.yml` — the `versatiles:` candy entity (description, `require`,
  `distro`, `port`, `env_provide`, `service`, `plan`) and its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-versa:versatiles` — the owning skill: the layer properties, the URL
  surface, the `convert` subcommand, and the supervisord restart race. Load
  before editing, building, deploying, or troubleshooting this candy.
- `/charly-versa:shortbread` — produces the PMTiles files versatiles serves.
- `/charly-versa:versatiles-frontend` — the SPA for exploring tile content.
- `/charly-versa:versa` — the image composing this layer.
- `/charly-pod:pod` — the `kind: pod` / deploy schema reference (this candy is
  composed into a box; services, ports).
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `run:` / `write:` / `check:`, service declarations).
- `/charly-check:check` — the check/R10 framework (`charly check box`,
  `charly check run <bed>`).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The candy's own `check:` steps assert the installed binary, its version, the
  linked `convert` subcommand, and the serve wrapper, and — at deploy scope — the
  running service, the reachable port, an HTTP 200 on a known OSM tile endpoint,
  and the PMTiles round-trip.

## Modify this repo

- Edit the `versatiles:` candy entity in `charly.yml`; the `skill:` entity in the
  same file is the owning skill's source — a candy change and its skill change
  land together.
- The install step resolves the release asset dynamically from the GitHub API
  (`curl` + `jq`, matching `versatiles-linux-gnu-x86_64.tar.gz`); keep the
  `distro` build deps in step.
- The serve wrapper always includes the public `download.versatiles.org`
  source and appends local containers; keep the `mkdir -p` defensive pattern —
  `versatiles serve` refuses an empty directory.
- The `skill:` entity is the source for `/charly-versa:versatiles`; never edit
  the generated `SKILL.md` — regenerate it.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
