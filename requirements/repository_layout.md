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

## Open

- [ ] The Specification Framework (`govstack:spec`, spec profile) has `chapters/` and
  `requirements/`, and its `toc.yml` lists `chapters`. Either the framework is a special case of
  the spec profile, or its requirement files move, or req-3 and req-5 change.
- [ ] `research/` exists in the GovStack Process and the Specification Framework. Is it part of the
  layout, or does req-8 warn for it?
- [ ] Repository settings (branch rule, tag rule) cannot be read from the files. Checking them
  needs the GitHub API and a token with admin read rights.
