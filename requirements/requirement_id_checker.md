# Requirement identifier checker

Keeps requirement identifiers stable (spec profile). A requirement keeps its identifier for the life of a specification: it is never removed, renumbered or reused. The rules are set in the Specification Framework, *Requirements*, sections *Unique identifier* and *Requirement status*. Under ARCH-ADR-5 (proposed), a requirement receives its number when it is accepted, and a number held by a withdrawn requirement is skipped for good. Under ARCH-ADR-6 (proposed), an option is numbered `n.m` beneath its variation point `n`, and its key is never changed or reused once it is published. Under ARCH-ADR-7 (proposed), the folder name of a conformance profile is its permanent id. The checks are set in the GovStack Process, chapter *Compiling Publications*, section *Requirement identifier checks*.

## Baseline

The baseline is the requirement index of the last released version, as published.

- requirement_id_checker#req-1: find the last released version with `records_client.md`: the highest version, by semantic version order, among the release records of the `urn`.
- requirement_id_checker#req-2: fetch `<url of that version>/requirements.json`. Fail if it cannot be fetched or does not have the shape in `renderer.md`.
- requirement_id_checker#req-3: fail if the `urn` and `version` in the fetched index are not the ones looked up.
- requirement_id_checker#req-4: never read the baseline from the repository or its git history.
- requirement_id_checker#req-5: if GovStack Records has no release record for the `urn`, there is no baseline. Run check 1 only, and report that there is no baseline.
- requirement_id_checker#req-6: if GovStack Records cannot be reached, or with `--offline`, skip every check against the baseline and report it as skipped. On release, a skipped baseline check is an error.

## Checks against the baseline

Run on pull request and on release.

- requirement_id_checker#req-7 (check 1): fail if two requirements of the specification have the same `id`. Report both files and lines.
- requirement_id_checker#req-8 (check 2): fail for every `id` in the baseline that is not in the specification, whatever its status in the baseline. The message says to mark it `DEPRECATED` instead of removing it.
- requirement_id_checker#req-9 (check 3): fail for every `id` not in the baseline that is not higher than the highest `id` in the baseline, counting the numbers of withdrawn requirements. Compare `id`s as integers. Options are checked by req-26.
- requirement_id_checker#req-10 (check 4): warn for every requirement that is `IMMUTABLE` in the baseline whose `statement` is different. Compare statements after collapsing white space. Show both statements in the message, for the reviewers to judge whether the intent changed.
- ~~requirement_id_checker#req-11~~: `DEPRECATED`. `observability` is no longer set in Building Block specifications, and a change of `interaction` is classified by whether it crosses `INTERNAL` (ARCH-ADR-8), see req-22.
- requirement_id_checker#req-17: fail for every requirement that is `PUBLISHED` or `DEPRECATED` in the baseline and has any other status now. A published requirement is never withdrawn and never returns to an unnumbered status, only deprecated (*Requirements*, *Requirement status*; ARCH-ADR-5).
- ~~requirement_id_checker#req-18~~: `DEPRECATED`. A functionality's profile is the folder its file is in (ARCH-ADR-7), see req-21 and req-23.
- requirement_id_checker#req-21: fail for every conformance profile id in the baseline that is not the id of a profile in the specification. The message says that a profile's folder is not renamed, because its name is the id cited in conformance declarations, and that its `title` may change instead. The implicit id `core` of the legacy layout (`conformance_profile_parser#req-1`) is exempt: when a specification moves to `conformance-profiles/`, its core takes the name of its folder, and that move is a patch release.
- requirement_id_checker#req-22: warn for every requirement whose `level` or `mutability` changed since the baseline, and name the release type the change needs, from *Versioning and Change Documentation*, *Classifier changes*. Warn for every requirement whose `interaction` changed, and name the release type from ARCH-ADR-8: between `INTERNAL` and any other type, minor, because the verification method changes and GovMarket re-verifies the requirement; between two types other than `INTERNAL`, patch. In a specification read under `requirements_parser#req-63`, warn for every change of `observability` as *Classifier changes* says.
- requirement_id_checker#req-23: warn for every functionality whose conformance profile changed since the baseline, and for every extension profile added, or whose `requires` gained an id. Match functionalities by `title`. Name the release type from *Functionalities and Conformance Profiles*, *Versioning*: adding an extension is minor; moving a functionality from the core into an extension, from an extension into the core, or between extensions, and adding a dependency between extensions, is major. The message says to record the move in the changelog.
- requirement_id_checker#req-24: warn for every change to a variation point, an option or a condition since the baseline, and name the release type from *Requirements*, *Options and conditions*, *Versioning* (ARCH-ADR-6). For a `REQUIRED` variation point: an option newly `PUBLISHED` is minor; a published option newly `DEPRECATED` is major; a change of `select.min` or `select.max` is major. A condition added to or removed from a published `REQUIRED` requirement, in `applies_when` or `applies_when_all` after `requirements_parser#req-54`, is major. The same changes to a `RECOMMENDED` variation point or requirement are minor.
- requirement_id_checker#req-25: fail for every option in the baseline whose `key` is different or missing now, and for every key in the baseline that is now the key of another option. A published key is never changed or reused (ARCH-ADR-6, *Keys*).
- requirement_id_checker#req-26 (check 3, options): fail for every option `id` not in the baseline whose number after the full stop is not higher than the highest option number of the same variation point in the baseline, counting withdrawn options.
- ~~requirement_id_checker#req-19~~: `DEPRECATED`. The changes of req-11 and req-18 are replaced, see req-28.
- requirement_id_checker#req-28: compute the minimum release type from the changes found by req-8 to req-10, req-17 and req-21 to req-26, and from the status changes in *Versioning and Change Documentation*, *Classifier changes*: a newly `PUBLISHED` or newly `DEPRECATED` `REQUIRED` requirement is major, a `RECOMMENDED` one minor. Report it on pull request. On release, fail if the version being released is less significant than the minimum (*Versioning and Change Documentation*, *Checking the release type*). Changes to capabilities never raise the minimum: they are informative. Removing `observability` from a specification changes no requirement and does not raise it (ARCH-ADR-8).

## Checks against the default branch

Run on pull request only. Two pull requests that take the same free number cannot both merge.

- requirement_id_checker#req-12: read the requirements on the default branch of the repository, with `requirements_parser.md`.
- requirement_id_checker#req-13: run check 1 on the pull request.
- requirement_id_checker#req-14: fail for every `id` that is in the pull request and not on the default branch, and is not higher than the highest `id` on the default branch. Options are checked by req-27.
- requirement_id_checker#req-15: if the default branch cannot be parsed, warn and skip req-14.
- requirement_id_checker#req-27: apply req-26 against the default branch: fail for every option `id` that is in the pull request and not on the default branch, and whose number is not higher than the highest option number of the same variation point on the default branch.
- requirement_id_checker#req-20: fail for every requirement that has an `id` on the default branch and, in the pull request, has disappeared or has no `id`. This is how a number issued at acceptance stays out of reuse when the requirement is withdrawn before release (ARCH-ADR-5).

## Drafts and release candidates

- requirement_id_checker#req-16: never use a pre-release version as a baseline. Release records are only written for versions without a pre-release suffix (`release.md`), so req-1 finds none.

## Open

- [ ] Migrating existing specifications: a specification released before the compiler has no `requirements.json`, so req-2 fails. (*Compiling Publications*, *To be decided*.)
- [ ] req-1 takes the highest released version. If a Working Group releases a patch of an older line, for example 1.1.1 after 1.2.0, the baseline is still 1.2.0. Is that right?
- [ ] *Requirements* leaves open whether Service API and cross-functional requirements have sequences of their own (`#req-`, `#cfr-`). These checks assume one sequence.
- [ ] req-28 does not yet compare OpenAPI files. *Versioning and Change Documentation*, *Interface changes*, classifies changes to operations referred to by `SERVICE_API` requirements, and a removed operation sets a floor of major.
- [ ] Keys of options without an `id` are not in the baseline (`renderer#req-23`), so req-25 cannot see a key changed before its option is numbered. ARCH-ADR-6 says keys are fixed "once the option is published", which req-25 covers.
- [ ] Statement changes cannot be classified by comparison. req-10 reports them for `IMMUTABLE` requirements only; *Versioning and Change Documentation* asks for every changed statement to be reported to the release review.
