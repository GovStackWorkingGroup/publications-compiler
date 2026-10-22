# Terminology parser

Reads `terminology.yml`, the term entries of a publication. The file format is the one used in
the GovStack Process, `introduction/terminology.yml`. What makes a term entry well formed is set in
Crafting GovStack Terminologies, *Structure of a Term entry*. In the terminology profile every
check below is an error unless it says warn. In other profiles every check is a warning.

```yaml
terms:
  - term: reference model
    abbreviation:
    definition: >-
      abstract framework for understanding significant relationships among the entities of some
      environment
    origin: adopted # adopted, adapted, govstack
    source: OASIS, Reference Model for Service Oriented Architecture 1.0, 1.1.
    notes:
      - <note to entry>
    related: [reference architecture]
```

## Entries

- terminology_parser#req-1: read `terminology.yml` as YAML. Fail if it does not parse, or has no
  `terms` list. An empty file is a terminology with no entries.
- terminology_parser#req-2: fail if an entry has no `term` or no `definition`.
- terminology_parser#req-3: fail if two entries have the same `term`, compared without regard to
  case.
- terminology_parser#req-4: fail if `origin` is present and not one of `adopted`, `adapted`,
  `govstack`. Treat a missing `origin` as `govstack`.
- terminology_parser#req-5: fail if `origin` is `adopted` or `adapted` and `source` is empty.
  *Structure of a Term entry*, data category 11.
- terminology_parser#req-6: fail if `origin` is `adapted` and no note says how the definition was
  modified.
- terminology_parser#req-7: fail if a name in `related` is not the `term` of an entry in the same
  file.
- terminology_parser#req-8: warn for every key other than `term`, `abbreviation`, `definition`,
  `origin`, `source`, `notes` and `related`.

## Written form

The machine-checkable rules of *Structure of a Term entry*, *Terms and their normative status*,
rule 5, and the definition rules of ISO 704.

- terminology_parser#req-9: warn if `term` starts with an article (`a`, `an`, `the`).
- terminology_parser#req-10: warn if `term` starts with a capital letter and is not an
  abbreviation or a proper name. Keep a list of allowed capitalised terms per publication.
- terminology_parser#req-11: fail if `term` holds parentheses or square brackets used to show an
  alternative term, such as `term (alt)`.
- terminology_parser#req-12: warn if `definition` starts with an article or ends with a full stop.
- terminology_parser#req-13: warn if `definition` starts with the `term` itself (a circular
  definition).

## Output

- terminology_parser#req-14: pass to the renderer the entries in the order of the file, each with
  an entry number. Entry numbers follow the position of the entry in the file.

## Open

- [ ] *Structure of a Term entry* makes the entry number mandatory and stable between versions
  (*Entry number*). The YAML format has no number field, so numbers change when entries are
  reordered. Add an `id` field and check it with rules like `requirement_id_checker.md`?
- [ ] The YAML format has no field for admitted terms, deprecated terms, subject field or examples,
  which *Structure of a Term entry* lists as data categories 3, 4, 6 and 9.
- [ ] `terminologies/resources/template.yml` is empty. It should hold the canonical format.
