# Release

What the compiler does when a version is released: it confirms the release, checks the
publication, renders the book and opens a pull request to GovStack Records. The process is
set in the GovStack Process, chapter *Compiling Publications*, sections *Releasing a version* and
*Outputs*. Records are set in chapter *GovStack Records*.

## Confirming the release

Before any check, on the `release` event.

- release#req-1: fail if the tag does not start with `v`.
- release#req-2: fail if the tag without `v` is not a `version` in `metadata.yml`.
- release#req-3: fail if that version does not have `latest: true`.
- release#req-4: if the version has a pre-release suffix, check the publication and stop. Do not
  render and do not write to GovStack Records. Report that a pre-release is checked, not published.
- release#req-5: fail if that version has an empty `release_date` or `short_description`. Warn if
  `release_date` is not the date of the GitHub release.
- release#req-6: fail if GovStack Records already has a release record for the same `urn` and
  `version`.

## Order

- release#req-7: run in this order: confirm the release, run every check, render the book, deploy
  the book, open the records pull request. Stop at the first step that fails.
- release#req-8: open the records pull request only after the book is served at its URL, so that
  the release record never points to a page that does not exist.
- release#req-9: when a step fails, report every failed check and the step that failed, on the
  GitHub release and in the job summary, so that the Editorial Committee is told why.

## The records pull request

- release#req-10: open one pull request to `GovStackWorkingGroup/govstack-records` that adds the
  release record and updates the person records (`authors_compiler.md`).
- release#req-11: write the release record to
  `records/releases/<YYYY-MM-DD>-<name>-<version>.yml`, where `<YYYY-MM-DD>` is the date of the
  GitHub release and `<name>` is the `urn` without `govstack:` and with hyphens for colons. For
  example `records/releases/2026-06-14-spec-bb-wallet-1.2.0.yml`.
- release#req-12: write the release record with these fields and no others:

  ```yaml
  ---
  urn: govstack:spec:bb:wallet
  version: 1.2.0
  url: https://specs.govstack.global/bb-wallet/1.2.0
  release: https://github.com/GovStackWorkingGroup/bb-wallet/releases/tag/v1.2.0
  date: 2026-06-14
  decided_by: govstack:team:editorial-committee
  ```

  `url` is the site root followed by `url_suffix`.
- release#req-13: never change or delete an existing record file.
- release#req-14: name the branch `release/<name>-<version>`, and title the pull request
  `Release <urn> <version>`. Link the GitHub release in its description.
- release#req-15: if a pull request for the same branch is already open, update it instead of
  opening another.

## Open

- [ ] *Compiling Publications* step 3 says "a member of the Technical Committee publishes a GitHub
  Release", and the tag rule says only the Editorial Committee can create `v*` tags. Which one?
- [ ] The token that opens the records pull request. A GitHub App installed on the organisation
  is the usual answer. A reusable workflow does not get the secrets of `publications-compiler`.
  It only gets the caller's secrets, see `workflow.md`.
- [ ] *Compiling Publications* says the records pull request carries only the release record and
  person records. `publications.yml` is regenerated from the records by another tool. Confirm that
  the compiler never edits it.
