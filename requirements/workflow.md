# Workflow

The compiler runs as a GitHub Action. Its code is kept in this repository, so that a change to a
rule applies to every publication at once. Each publication calls it from a short workflow. Set in
the GovStack Process, chapter *Compiling Publications*, section *When the compiler runs*.

```yaml
# .github/workflows/compile.yml in a publication repository
name: Compile
on:
  pull_request:
  release:
    types: [published]
jobs:
  compile:
    uses: GovStackWorkingGroup/publications-compiler/.github/workflows/compile.yml@v1
```

## The reusable workflow

- workflow#req-1: provide `.github/workflows/compile.yml` in this repository, triggered by
  `workflow_call`.
- workflow#req-2: check out the publication repository at the commit of the event, with enough
  history to read the default branch (`requirement_id_checker#req-12`).
- workflow#req-3: run the compiler with the event name and, on release, the release tag
  (`index#req-5`).
- workflow#req-4: run the compiler from the same tag of this repository as the workflow, so that
  `@v1` pins both the workflow and the code.
- workflow#req-5: on `pull_request`, ask for read permissions only.
- workflow#req-6: on `release`, ask for the permissions needed to deploy the book and to comment
  on the release, and use the records credentials only in the step that opens GovStack Records pull
  request.
- workflow#req-7: fail the job when the compiler exits with status 1 or 2.

## Versions of the compiler

- workflow#req-8: tag every release of the compiler with a full version, `vMAJOR.MINOR.PATCH`, and
  move the tag `vMAJOR` to it.
- workflow#req-9: release a new major version for any change that turns a warning into an error,
  adds an error, or changes an output format (`requirements.json`, records in GovStack Records). Publications
  then move to it on their own schedule.

## Running locally

- workflow#req-10: run the same compiler from the command line on a local checkout, with the same
  checks and output as the action (`index#req-12`).

## Open

- [ ] The caller workflow above passes no secrets. The release job needs credentials to deploy and
  to open the records pull request. Either the caller adds `secrets: inherit` and the
  organisation holds the secrets, or a GitHub App supplies them. The `compile.yml` in
  *Compiling Publications* and in `bb-template` changes in the first case.
- [ ] workflow#req-9 means a new error reaches publications only when they move to the new major
  version. *Compiling Publications* says moving `v1` "updates the compiler for every publication at
  once". Decide which changes are allowed under the same major version.
