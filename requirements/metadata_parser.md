# Metadata parser

Reads `metadata.yml`, which identifies the publication and lists its versions. It also decides which
version is being compiled. The format is set in the GovStack Process, chapter *Compiling
Publications*, section *metadata.yml*. The identifier rules are set in chapter *GovStack
Namespaces*.

## Publication fields

- metadata_parser#req-1: read `metadata.yml` as YAML. Fail if it does not parse or is not a map.
- metadata_parser#req-2: fail if any of `name`, `abstract`, `url`, `repository`, `urn`,
  `owner_group` or `versions` is missing or empty.
- metadata_parser#req-3: warn for every top-level key not listed in req-2.
- metadata_parser#req-4: warn if `name` ends with the name of its publication type, such as
  `Building Block Specification`. The compiler adds the type when it renders the title.
- metadata_parser#req-5: fail if `url` or `repository` is not an absolute `https` URL.
- metadata_parser#req-6: fail if `repository` is not the repository being compiled. On GitHub, the
  repository is read from `GITHUB_REPOSITORY`, and the two are compared without regard to case.
- metadata_parser#req-7: fail if `urn` does not match
  `govstack:<class>[:<node>]:<name>` or the bare `govstack:<class>`, where each segment uses only
  lowercase letters, digits and hyphens.
- metadata_parser#req-8: fail if `urn` is in a retired namespace: `govstack:core:`,
  `govstack:specs:`, `govstack:bb:`, `govstack:meta`, `govstack:spec:process`, `govstack:arch:`.
  The message names the replacement given in *GovStack Namespaces*, *Retired namespaces*.
- metadata_parser#req-9: fail if `urn` is not in a publication namespace listed in *GovStack
  Namespaces*, *Publication namespaces*.
- metadata_parser#req-10: fail if `owner_group` does not match `govstack:wg:<id>` or
  `govstack:team:<id>`.

## Versions

- metadata_parser#req-11: fail if `versions` is not a list with at least one entry.
- metadata_parser#req-12: fail if a version entry has no `version`, or if two entries have the same
  `version`.
- metadata_parser#req-13: fail if a version entry has no `release_date` key or no
  `short_description` key. Both keys may have an empty value.
- metadata_parser#req-14: fail if a `release_date` is not empty and is not `YYYY-MM` or
  `YYYY-MM-DD`.
- metadata_parser#req-15: fail unless exactly one entry has `latest: true`. Warn for every entry
  with `latest: false`: on every entry but one, `latest` is omitted.
- metadata_parser#req-16: parse each `version` as `MAJOR.MINOR[.PATCH][-SUFFIX]`. Fail if it does
  not parse. A version with a `SUFFIX` is a pre-release.
- metadata_parser#req-17: fail if a version without a pre-release suffix has no `url_suffix`.
- metadata_parser#req-18: fail if a `url_suffix` does not start with `/` or ends with `/`.
- metadata_parser#req-19: warn if a version entry has a key not listed in *Compiling Publications*.

## The version being compiled

- metadata_parser#req-20: on `pull_request`, compile the version marked `latest`.
- metadata_parser#req-21: on `release`, compile the version whose `version` is the release tag
  without its leading `v`. The checks on the tag are in `release.md`.
- metadata_parser#req-22: pass to the later stages: the publication fields, the version being
  compiled, whether it is a pre-release, and the profile read from `urn` (see `profiles.md`).

## Open

- [ ] *Compiling Publications* gives `1.0-draft` and `2.0.0-rc` as versions. `1.0-draft` is not
  valid semantic versioning, which the Specification Framework requires (*Use semantic
  versioning*). req-16 accepts both forms until this is settled.
- [ ] `bb-template` writes `urn: govstack:spec:<type>:<name>` and `owner_group:
  govstack:team:editorial-committee`. A repository created from the template fails req-7 until it
  is filled in. Should the compiler report placeholders such as `<name>` with a clearer message?
- [ ] Should the compiler check that `url` resolves as *GovStack Namespaces* says it should, for
  example `https://specs.govstack.global/bb-<name>/` for `govstack:spec:bb:<name>`?
