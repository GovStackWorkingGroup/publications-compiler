# README

This repository is a compiler tool that takes a GovStack repository as input and checks and compiles it.

The rules the compiler implements are set in other publications. This folder states what the
compiler does to implement them. Each requirement cites its source.

| Source | Sets |
|---|---|
| GovStack Process, chapter *Compiling Publications* | Repository layout, `metadata.yml`, `authors.yml`, `toc.yml`, profiles, requirement identifier checks, release, outputs |
| GovStack Process, chapter *GovStack Namespaces* | Identifier syntax, publication namespaces, retired namespaces, person identifiers |
| GovStack Process, chapter *GovStack Records* | Records files, release and person records |
| Specification Framework, *Structure of Specifications* and *Requirements Model* | Requirement files and classifiers (spec profile) |
| Crafting GovStack Terminologies, *Structure of a Term entry* | Term entries (terminology profile) |

## Requirement files

| File | Covers |
|---|---|
| `index.md` | Pipeline, inputs, diagnostics, milestones |
| `repository_layout.md` | Folders and files at the root of a publication |
| `metadata_parser.md` | `metadata.yml` and the version being compiled |
| `authors_compiler.md` | `authors.yml` and the person records written to GovStack Records |
| `toc_parser.md` | `toc.yml` and the ordered list of pages |
| `page_parser.md` | Markdown pages, front matter, YAML pages, links and assets |
| `profiles.md` | Choosing the profile from the `urn`, and the checks of each profile |
| `requirements_parser.md` | Requirement files of a specification (spec profile) |
| `requirement_id_checker.md` | Requirement identifier stability against the baseline (spec profile) |
| `terminology_parser.md` | `terminology.yml` term entries |
| `records_client.md` | Reading GovStack Records |
| `renderer.md` | The book and the requirement index |
| `release.md` | Release gating and the records pull request |
| `workflow.md` | The GitHub Action that runs the compiler |

## Requirement format

- index#req-3: write every requirement of the compiler as `<file>#req-<n>: <statement>`, where
  `<file>` is the name of the requirement file without `.md`.
- index#req-4: never renumber or reuse a requirement number in this folder. A requirement that no
  longer applies is struck through and marked `DEPRECATED`.
- ~~index#req-1~~: `DEPRECATED`. Moved to `repository_layout.md`.
- ~~index#req-2~~: `DEPRECATED`. Moved to `metadata_parser.md`.

## Pipeline

The compiler runs these stages in order. A stage reads the output of the stages before it.

```text
repository ─► layout ─► metadata ─► authors ─► toc ─► pages ─► profile checks ─► render ─► records PR
                                                                    ▲                        (release only)
                                                    records ────────┘
```

- index#req-5: take as input the path to a checked-out publication repository, and the event that
  started the run: `pull_request` or `release`. On `release`, also take the release tag.
- index#req-6: run every check of every stage before stopping, so that one run reports every
  failure. A stage whose input could not be read is skipped, and the compiler reports that it was
  skipped and why.
- index#req-7: on `pull_request`, check only. Do not render and do not write to GovStack Records.
- index#req-8: on `release`, render and write to GovStack Records only if no check reported an error.
- index#req-9: run without network access when given the flag `--offline`. Checks that need the
  GovStack Records or the published baseline are skipped and reported as skipped, not as passed.

## Diagnostics

- index#req-10: report each failed check as a diagnostic with a severity (`error` or `warning`),
  the requirement of this folder it fails (for example `toc_parser#req-3`), a message, and the file
  and line it concerns when there is one.
- index#req-11: exit with status 0 when no diagnostic is an error, 1 when at least one is, and 2
  when the compiler itself failed.
- index#req-12: print diagnostics as text by default, and as JSON when given `--format json`.
- index#req-13: when run as a GitHub Action, also emit each diagnostic as a GitHub workflow
  annotation on its file and line, and write a summary of the run to the job summary.
- index#req-14: keep the severity of each check in one table in the compiler's code, so that
  turning a warning into an error is a change in one place (*Compiling Publications*, "Which checks
  are errors and which are warnings is defined there").

## Milestones

### First milestone: Publication rendering

Check and render any publication on a pull request, without GovStack Records.

- `repository_layout.md`, `metadata_parser.md`, `authors_compiler.md` (parsing only),
  `toc_parser.md`, `page_parser.md`, `renderer.md` (book only).
- `profiles.md`: profile selection, and the process, ra and guide profiles.
- `workflow.md`: the pull request job.

### Second milestone: Specifications and terminologies

- `requirements_parser.md`, `terminology_parser.md`.
- `profiles.md`: spec and terminology profiles.
- `renderer.md`: the requirement index, `requirements.json`.
- `requirement_id_checker.md`: checks 1 and 3 against the default branch, and the first-release
  case. These need no GovStack Records.

### Third milestone: Records and release

- `records_client.md`, `release.md`.
- `requirement_id_checker.md`: checks against the published baseline.
- `authors_compiler.md`: person records.
- `profiles.md`: the common checks that read GovStack Records (proposal record, owner group,
  obsoletion).
- `workflow.md`: the release job.

## Open

- [ ] Implementation language. The compiler runs as a reusable GitHub Action, but should also run
  locally so that a Working Group can check before it pushes.
- [ ] Where the rendered book is deployed, and by what. *Compiling Publications* says the book is
  served at `url` + `url_suffix` but not which host serves it.
- [ ] The rendering engine (static site generator), see `renderer.md`.
