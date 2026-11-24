# Decisions

Each decision is numbered and never renumbered. A decision that is replaced is struck through and points to the one that replaces it.

## D1: The checker is written in Python

Taken 2026-10-29.

Everything that reads, checks and compiles a publication is Python: parsing, every check, diagnostics, `requirements.json`, the records pull request. It runs the same from the command line and in GitHub Actions (`workflow#req-10`).

- Python 3.12 or later. `uv` for environments and lock files, `ruff` for lint and format, `pytest` for tests.
- `ruamel.yaml` for every YAML file. It reports the line of each key, which diagnostics need (`index#req-10`), and writes YAML back without reformatting it, which `persons.yml` needs (`authors_compiler#req-17`).
- `markdown-it-py` to find links, images and headings in pages (`page_parser.md`). The checker parses Markdown to check it, not to render it.
- `jsonschema` to validate the bundle and `requirements.json` against their schemas.

Resolves: *Implementation language* in `readme.md`, *Open*.

## D2: The book is rendered with Astro Starlight and served on GitHub Pages

Taken 2026-10-29.

The renderer is an Astro Starlight project. GitHub Pages is the first host we deploy to.

Consequences to work out (see `work-plan.md`, phase 0):

- Starlight has no built-in versioning. Each version of a publication is a separate build with its own base path (the path of the version URL, `metadata_parser#req-17`), and the version list (`renderer#req-8`) is a custom component.
- A GitHub Pages site deployed with `actions/deploy-pages` replaces the whole site on each deploy. Keeping earlier versions online (`renderer#req-3`, `req-4`) needs either a `gh-pages` branch that accumulates versions, or a build that rebuilds or restores every released version.
- A publication's `url` is `https://specs.govstack.global/...`. GitHub Pages serves one custom domain per repository, so either one repository serves all publications, or each publication has its own domain or path.
- Mermaid needs a plugin (`renderer#req-11`).

Resolves in part: *The rendering engine* and *Where the rendered book is deployed* in `readme.md`, *Open*.

## D3: Checking and rendering are separate projects

Taken 2026-10-29.

The checker and the renderer are separate projects in this repository. They meet only at the **bundle**: a folder written by the checker and read by the renderer, whose shape is set by a JSON Schema in `schemas/`. The renderer never reads a publication repository, and the checker knows nothing about Starlight. Replacing Starlight means writing another renderer that reads the same bundle.

See `architecture.md`.

Consequences for the requirements:

- `requirements.json` is data, not presentation. The checker writes it (`renderer#req-21` to `req-23`, `req-44`, and `req-45` in part; `req-20` and `req-25` before ARCH-ADR-7), and the renderer only links to it. Move these requirements, or say in `renderer.md` that they belong to the checker.
- Link rewriting (`page_parser#req-10`) happens in the checker, to paths the bundle defines. The renderer maps bundle paths to its own URLs.
- `renderer.md` gains a requirement: render from the bundle alone.

## D4: CI/CD runs on GitHub Actions

Taken 2026-10-29.

The compiler's own CI (lint, tests, schema checks, renderer build) and the reusable workflow that publications call (`workflow.md`) are GitHub Actions workflows in `.github/workflows/`.

## D5: The pilot is bb-file-management, branch v0.3-(new-spec-framework)

Taken 2026-10-29.

`GovStackWorkingGroup/bb-file-management`, branch `v0.3-(new-spec-framework)`, is the first publication the compiler runs on. See `pilot-bb-file-management.md`.

## Open

- [ ] D6: whether `version` must be full semantic versioning (`MAJOR.MINOR.PATCH[-SUFFIX]`). See `pilot-bb-file-management.md`, *Version*.
- [ ] D7: how earlier versions stay online on GitHub Pages (D2).
- [ ] D8: which repository serves the books, and under which domain (D2).
