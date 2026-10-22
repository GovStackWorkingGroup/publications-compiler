# Profiles

A profile is the set of checks the compiler applies to a type of publication. The compiler reads
the `urn` and applies the profile of its namespace. Profiles are set in the GovStack Process,
chapter *Compiling Publications*, section *Compilation profiles*. Namespaces are set in *GovStack
Namespaces*.

## Selecting the profile

- profiles#req-1: choose the profile from the `urn`:

  | `urn` | Profile |
  |---|---|
  | `govstack:process` | process |
  | `govstack:ra:<name>` | ra |
  | `govstack:spec`, `govstack:spec:<...>` | spec |
  | `govstack:guide:<name>` | guide |
  | `govstack:terminology`, `govstack:terminology:<...>` | terminology |

- profiles#req-2: fail if no profile matches. Do not fall back to a default profile.
- profiles#req-3: keep the table in req-1 in one place in the code, so that a new namespace is
  one entry.

## Common checks

Every profile runs these checks. They are listed in *Compiling Publications*, *Common checks*.

| # | Check | Implemented by |
|---|---|---|
| 1 | Repository layout | `repository_layout.md` |
| 2 | `metadata.yml` complete, exactly one `latest` | `metadata_parser.md` |
| 3 | `urn` has an approved proposal and is not retired | profiles#req-4, `metadata_parser#req-8` |
| 4 | `owner_group` is in GovStack Records | profiles#req-5 |
| 5 | Not obsolete (release only) | profiles#req-6 |
| 6 | `authors.yml` complete | `authors_compiler.md` |
| 7 | `toc.yml` valid | `toc_parser.md` |

- profiles#req-4: fail if `records_client.md` finds no proposal record with `decision: approved`
  for the `urn`.
- profiles#req-5: fail if `owner_group` is not a Working Group or team in GovStack Records. Warn if its
  `status` is `inactive`.
- profiles#req-6: on release, fail if GovStack Records has an obsoletion record for the `urn`.
- profiles#req-7: skip req-4 to req-6 with `--offline`, and report them as skipped.

## process and guide profiles

- profiles#req-8: the process profile adds no check.
- profiles#req-9: the guide profile fails if a page has a `requirements` key in its front matter.

## ra profile

- profiles#req-10: fail if a page has a `requirements` key in its front matter.
- profiles#req-11: warn if a sentence in a page uses `SHALL`, `MUST`, `REQUIRED` or `SHALL NOT` in
  capitals. A reference architecture is informative and states no requirements.

## spec profile

- profiles#req-12: parse every file under `requirements` in `toc.yml` with
  `requirements_parser.md`.
- profiles#req-13: run `requirement_id_checker.md`.
- profiles#req-14: render the requirement index (`renderer.md`).

## terminology profile

- profiles#req-15: parse every term entry with `terminology_parser.md`, in the files of
  `chapters` and `introduction` that hold term entries.

## Open

- [ ] Check 3 needs proposal records, and GovStack Records has none yet. Until they are backfilled
  (*GovStack Records*, *Open*), every publication fails check 3. Should check 3 be a warning
  until the backfill is done?
- [ ] req-11 (ra) and req-9 (guide) look for requirements by keyword. Is a keyword check wanted,
  or only the front matter check?
- [ ] The terminology profile: where do shared terminologies keep their entries? The publication
  *Crafting GovStack Terminologies* has `chapters/requirements.yml` and `resources/template.yml`
  (empty).
