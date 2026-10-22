# Requirement identifier checker

Keeps requirement identifiers stable (spec profile). A requirement keeps its identifier for the
life of a specification: it is never removed, renumbered or reused. The rules are set in the
Specification Framework, *Requirements Model*, 5.3.1. The checks are set in the GovStack Process,
chapter *Compiling Publications*, section *Requirement identifier checks*.

## Baseline

The baseline is the requirement index of the last released version, as published.

- requirement_id_checker#req-1: find the last released version with `records_client.md`: the
  highest version, by semantic version order, among the release records of the `urn`.
- requirement_id_checker#req-2: fetch `<url of that version>/requirements.json`. Fail if it cannot
  be fetched or does not have the shape in `renderer.md`.
- requirement_id_checker#req-3: fail if the `urn` and `version` in the fetched index are not the
  ones looked up.
- requirement_id_checker#req-4: never read the baseline from the repository or its git history.
- requirement_id_checker#req-5: if GovStack Records has no release record for the `urn`, there is no
  baseline. Run check 1 only, and report that there is no baseline.
- requirement_id_checker#req-6: if GovStack Records cannot be reached, or with `--offline`, skip every
  check against the baseline and report it as skipped. On release, a skipped baseline check is an
  error.

## Checks against the baseline

Run on pull request and on release.

- requirement_id_checker#req-7 (check 1): fail if two requirements of the specification have the
  same `id`. Report both files and lines.
- requirement_id_checker#req-8 (check 2): fail for every `id` in the baseline that is not in the
  specification, whatever its status in the baseline. The message says to mark it `DEPRECATED`
  instead of removing it.
- requirement_id_checker#req-9 (check 3): fail for every `id` not in the baseline that is not
  higher than the highest `id` in the baseline. Compare `id`s as integers.
- requirement_id_checker#req-10 (check 4): warn for every requirement that is `IMMUTABLE` in the
  baseline whose `statement` is different. Compare statements after collapsing white space. Show
  both statements in the message, for the reviewers to judge whether the intent changed.
- requirement_id_checker#req-11: warn for every requirement whose `level`, `observability` or
  `mutability` changed since the baseline. *Requirements Model* 5.3.3 to 5.3.5: a change of
  classifier needs a new version number.

## Checks against the default branch

Run on pull request only. Two pull requests that take the same free number cannot both merge.

- requirement_id_checker#req-12: read the requirements on the default branch of the repository,
  with `requirements_parser.md`.
- requirement_id_checker#req-13: run check 1 on the pull request.
- requirement_id_checker#req-14: fail for every `id` that is in the pull request and not on the
  default branch, and is not higher than the highest `id` on the default branch.
- requirement_id_checker#req-15: if the default branch cannot be parsed, warn and skip req-14.

## Drafts and release candidates

- requirement_id_checker#req-16: never use a pre-release version as a baseline. Release records
  are only written for versions without a pre-release suffix (`release.md`), so req-1 finds none.

## Open

- [ ] Migrating existing specifications: a specification released before the compiler has no
  `requirements.json`, so req-2 fails. (*Compiling Publications*, *To be decided*.)
- [ ] req-1 takes the highest released version. If a Working Group releases a patch of an older
  line, for example 1.1.1 after 1.2.0, the baseline is still 1.2.0. Is that right?
- [ ] *Requirements Model* leaves open whether Service API and cross-functional requirements have
  sequences of their own (`#req-`, `#cfr-`). These checks assume one sequence.
