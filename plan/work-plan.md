# Work plan

## Phase 0: Decisions

- [x] Checker language (D1).
- [x] Rendering engine and first host (D2).
- [x] Separation of checking and rendering (D3).
- [x] CI/CD on GitHub Actions (D4).
- [x] Pilot publication (D5).
- [ ] Full semantic versioning for `version` (D6).
- [ ] Keeping earlier versions online on GitHub Pages (D7).
- [ ] Repository and domain that serve the books (D8).
- [ ] Update `renderer.md` for D3: render from the bundle alone; move `requirements.json` to the checker.
- [ ] Update `readme.md`, *Open*, for D1 and D2.

## Phase 1: Skeleton

- [ ] `checker/`: `pyproject.toml`, `uv` lock, `ruff`, `pytest`, a `govstack-compiler` command.
- [ ] Command line: repository path, `--event`, `--tag`, `--offline`, `--format`, `--render`, `--records` (`index#req-5`, `req-9`, `req-12`; `renderer#req-2`; `records_client#req-1`).
- [ ] Diagnostics: severity, requirement, message, file, line (`index#req-10`). Text and JSON output (`index#req-12`). Exit codes (`index#req-11`).
- [ ] One severity table (`index#req-14`).
- [ ] Stage runner: stages in order, skipped stages reported with their reason (`index#req-6`).
- [ ] YAML loading with line numbers, shared by every parser.
- [ ] Test fixtures: a pinned snapshot of the pilot, and small repositories that each fail one check.
- [ ] `schemas/bundle.schema.json`, first version.
- [ ] `renderer/`: Starlight project that builds a hand-written sample bundle, with the base path set from the bundle.
- [ ] `.github/workflows/ci.yml`: lint and test the checker, validate the sample bundle, build the renderer.

## Phase 2: Milestone 1, check and render a publication

- [ ] `repository_layout#req-1` to `req-17`, except `req-3` and `req-11` (deprecated).
- [ ] `metadata_parser#req-1` to `req-22`.
- [ ] `profiles#req-1` to `req-3`, `req-8` to `req-11`.
- [ ] `authors_compiler#req-1` to `req-8`.
- [ ] `toc_parser#req-1` to `req-27`.
- [ ] `page_parser#req-1` to `req-5`, `req-7` to `req-11`, `req-13`, `req-15` to `req-20`.
- [ ] `tag_parser#req-1` to `req-25`, `req-28` to `req-30`.
- [ ] Bundle writer.
- [ ] Renderer: front page, navigation from the bundle, version list, pages, assets, Mermaid, source links, references and folder pages (`renderer#req-3` to `req-12`, `req-14`, `req-15`, `req-26`), and tags (`renderer#req-27` to `req-34`).
- [ ] GitHub Actions annotations and job summary (`index#req-13`).
- [ ] Reusable workflow, pull request job (`workflow#req-1` to `req-5`, `req-7`).
- [ ] Deploy a preview of the pilot to GitHub Pages.
- [ ] Exit: the checker reports, on the pilot snapshot, the milestone 1 findings in `pilot-bb-file-management.md` and nothing else; the preview renders.

## Phase 3: Milestone 2, specifications and terminologies

- [ ] `metadata_parser#req-24`, `req-25`, `req-31`, `req-32`.
- [ ] `conformance_profile_parser.md`.
- [ ] `requirements_parser.md`: functionality fields, capabilities, requirement fields, statement (ARCH-ADR-5, ARCH-TDR-1).
- [ ] `requirements_parser.md`: verification from `interaction` (`req-60` to `req-64`; ARCH-ADR-8).
- [ ] `requirements_parser.md`: variation points, options and conditions (`req-41` to `req-58`, `req-65`; ARCH-ADR-6).
- [ ] `tag_parser#req-26`, `req-27`: `openapi`.
- [ ] `terminology_parser.md`, and `page_parser#req-14`.
- [ ] `profiles#req-12` to `req-15`.
- [ ] `requirements.json` and its schema (`renderer#req-21` to `req-23`, `req-44`, moved to the checker by D3).
- [ ] `requirement_id_checker#req-7`, `req-9`, `req-12` to `req-15`, `req-20`, `req-27`, and the first-release case (`req-5`).
- [ ] Renderer: conformance profiles, functionality files, requirements, variation points and conditions, views by key, requirement index, capabilities and profiles, terminology (`renderer#req-13`, `req-17`, `req-18`, `req-24`, `req-36` to `req-45`), and `openapi` operations (`renderer#req-35`).
- [ ] Fixture: a pinned snapshot of `bb-wallet` at commit `40ccc8c`, the first specification in the layout of ARCH-ADR-7 with the options of ARCH-ADR-6 (see `pilot-bb-file-management.md`, *Second fixture*).
- [ ] Exit: the checker reports, on the pilot snapshot, the milestone 2 findings in `pilot-bb-file-management.md`; the pilot renders in the legacy layout with its capabilities and draft requirements; the `bb-wallet` fixture renders with its four conformance profiles, its variation points and its views by key.

## Phase 4: Milestone 3, records and release

- [ ] `records_client.md`.
- [ ] `profiles#req-4` to `req-7`.
- [ ] `authors_compiler#req-9` to `req-17`.
- [ ] `requirement_id_checker.md`, checks against the published baseline, including conformance profiles, options and keys (`req-21` to `req-26`, `req-28`).
- [ ] `release.md`.
- [ ] Reusable workflow, release job (`workflow#req-6`), deploy to GitHub Pages.
- [ ] Compiler release tags (`workflow#req-8`).
