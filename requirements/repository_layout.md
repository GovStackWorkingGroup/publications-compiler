# Repository layout

Checks the folders and files at the root of a publication repository. The layout is set in the
GovStack Process, chapter *Compiling Publications*, section *Repository layout*.

```text
.
├── .github/        CODEOWNERS and the compile workflow
├── ADR/            decision records of the Working Group
├── assets/         images and other files the pages use
├── introduction/   overview, scope, references and terminology
├── chapters/       the body of the publication; or requirements/ if it is a specification
├── resources/      supporting material: APIs, tests, skills, scripts
├── metadata.yml    what the publication is
├── authors.yml     who wrote this version
└── toc.yml         which pages are rendered, and in which order
```

- repository_layout#req-1: fail if `metadata.yml`, `authors.yml` or `toc.yml` is missing.
- repository_layout#req-2: fail if any of the folders `ADR/`, `assets/`, `introduction/` or
  `resources/` is missing. A folder that holds only a `.keep` file counts as present and empty.
- repository_layout#req-3: in the spec profile, fail if `requirements/` is missing.
- repository_layout#req-4: outside the spec profile, fail if `chapters/` is missing.
- repository_layout#req-5: fail if both `chapters/` and `requirements/` hold a file that `toc.yml`
  lists. (See *Open*: the Specification Framework has both folders.)
- repository_layout#req-6: fail if `.github/CODEOWNERS` is missing, or does not name
  `@GovStackWorkingGroup/editorial-committee` as owner of `/metadata.yml`.
- repository_layout#req-7: fail if `.github/workflows/compile.yml` is missing, or does not call
  `GovStackWorkingGroup/publications-compiler/.github/workflows/compile.yml` on `pull_request` and
  on `release` of type `published`.
- repository_layout#req-8: warn for every file or folder at the root that is not in the layout
  above, except `README.md`, `LICENSE`, `CHANGELOG.md`, `RELEASE_NOTES.md`, and files and folders
  whose name starts with `.`.
- repository_layout#req-9: run before every other stage. Choose `chapters/` or `requirements/` from
  the profile, which `metadata_parser.md` reads from the `urn`. If the profile cannot be read, check
  everything except req-3 and req-4.

## File and folder names

The naming rule is set in the GovStack Process, chapter *Compiling Publications*, section *Repository layout*: "Files are named using `snake_case`, except for requirement files, which are named using `kebab-case`." Folder names do not follow it; a folder is allowed only if the layout above lists it.

A file name is checked without its extension. `snake_case` is lower-case letters and digits, separated by single underscores, starting with a letter, for example `data_model`. `kebab-case` is the same with single hyphens, for example `credential-intake`. The extension is lower case.

- repository_layout#req-10: fail for every file whose name is not `snake_case`, for example `Data Model.md`, `dataModel.md` or `data-model.md`. Give the `snake_case` name in the message.
- repository_layout#req-11: in the spec profile, fail for every requirement file (a Markdown file under `requirements/`) whose name is not `kebab-case`, instead of req-10. Give the `kebab-case` name in the message.
- repository_layout#req-12: leave out of req-10 and req-11 the files req-8 accepts at the root (`README.md`, `LICENSE`, `CHANGELOG.md`, `RELEASE_NOTES.md`), files under `ADR/` (their names are decision identifiers, such as `PROC-ADR-1.md`), and files and folders whose name starts with `.`, such as `.github/CODEOWNERS`.
- repository_layout#req-13: fail for every folder at the root that is not in the layout above, except folders whose name starts with `.`. Folder names are not checked against req-10. This replaces req-8 for folders; req-8 still warns for files. Folders inside the folders of the layout, such as `assets/diagrams/` or `resources/api/`, are free to name and are not checked.
- repository_layout#req-14: check only the files and folders tracked by git, so that a folder kept out of the repository, such as a local `research/`, is not checked, and a local run reports what a run on GitHub reports.

## Open

- [ ] Repository settings (branch rule, tag rule) cannot be read from the files. Checking them
  needs the GitHub API and a token with admin read rights.

## Deferred

- The Specification Framework (`govstack:spec`, spec profile) has `chapters/` and `requirements/`, and its `toc.yml` lists `chapters`. Either the framework is a special case of the spec profile, or its requirement files move, or req-3 and req-5 change. Its layout is not final, so this waits until it is.
