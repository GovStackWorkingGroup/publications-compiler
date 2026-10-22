# Authors compiler

Reads `authors.yml`, which lists the people credited in the version being compiled. On release, it
turns those people into person records for GovStack Records. The format is set in the
GovStack Process, chapter *Compiling Publications*, section *authors.yml*. Person identifiers are
set in *GovStack Namespaces*, *Person identifiers*. Person records are set in *GovStack Records*,
*Persons*.

## Parsing

- authors_compiler#req-1: read `authors.yml` as YAML. Fail if it does not parse, has no `authors`
  key, or `authors` is not a list with at least one entry.
- authors_compiler#req-2: fail if an entry has no `name`, no `roles` or no `urn`.
- authors_compiler#req-3: fail if `roles` is not a non-empty list whose values are each one of
  `Author`, `Editor`, `Contributor`.
- authors_compiler#req-4: fail if `urn` does not match `govstack:person:<id>`, where `<id>` uses
  only lowercase ASCII letters, digits and hyphens, and does not start or end with a hyphen.
- authors_compiler#req-5: fail if two entries have the same `urn`.
- authors_compiler#req-6: warn if `<id>` is not the form of `name` given in *Person identifiers*
  (lowercase, hyphens between words, accents removed), with or without a suffix `-2`, `-3` and so
  on. This is a warning, because an identifier does not change when a name changes.
- authors_compiler#req-7: fail if `url` is present, not empty, and not an absolute `https` URL.
- authors_compiler#req-8: warn for every key in an entry other than `name`, `roles`, `urn` and
  `url`.

## Checks against GovStack Records

These checks need `records_client.md`. They are skipped with `--offline`.

- authors_compiler#req-9: warn if an `<id>` is not in GovStack Records and another registered person
  has the same `name`. The person may already have an identifier ("Check GovStack Records first").
- authors_compiler#req-10: warn if an `<id>` is in GovStack Records with a different `name`. GovStack
  Records takes the new name on release.
- authors_compiler#req-11: fail if an `<id>` is in GovStack Records with `status: removed`. The
  compiler does not write a removed record again.

## Person records

On release only, and only if every check passed.

- authors_compiler#req-12: for every entry, add to `persons.yml` under `govstack: person:` a credit
  `{publication: <urn>, version: <version>, roles: <roles>}`.
- authors_compiler#req-13: create the person record with `id`, `name` and `url` if it does not
  exist.
- authors_compiler#req-14: set `name` to the name in `authors.yml`. Set `url` to the URL in
  `authors.yml` if one is given, and otherwise leave it unchanged.
- authors_compiler#req-15: write no other field to a person record.
- authors_compiler#req-16: do not add a credit that already exists for the same publication and
  version, so that running the release twice gives the same records.
- authors_compiler#req-17: keep the order and formatting of the rest of `persons.yml`, so that the
  pull request diff shows only the changed records.

## Open

- [ ] *Compiling Publications* says `authors.yml` credits "this version". `bb-template` says "the
  major version's authors". Which one?
- [ ] The Specification Framework's chapter files carry `authors:` in their front matter. Are
  those credited too, or only `authors.yml`?
