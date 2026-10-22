# Requirements parser

Reads the requirement files of a specification (spec profile). A requirement file describes one
functionality. Its requirements are in the YAML front matter, and its body describes the
functionality, its workflows and its data structures. The format is set in the Specification
Framework, *Structure of Specifications*, section *Requirement files*. The classifiers are set in
*Requirements Model*, 5.3.

```yaml
---
title: <functionality name>
capability: |
   <description from the business point of view>
cardinality: ALL # ALL, ONE_OF, ONE_OR_MORE
requirements:
  - id: '1'
    status: DRAFT # DRAFT, WITHDRAWN, CANDIDATE, ACCEPTED, PUBLISHED, DEPRECATED
    level: RECOMMENDED # RECOMMENDED, REQUIRED
    type: # FUNCTIONAL, WEBSERVICE, INTERFACE
    observability: # OBSERVABLE, AUDITABLE
    mutability: # IMMUTABLE, EXTENSIBLE, REPLACEABLE, INAPPLICABLE
    statement: <one sentence>
    documentation: <Markdown>
    api_operation_id:
    test:
---
```

## Functionality fields

- requirements_parser#req-1: fail if a file listed under `requirements` in `toc.yml` has no front
  matter, no `title`, or no `requirements` list.
- requirements_parser#req-2: warn if `capability` is missing or empty.
- requirements_parser#req-3: fail if `cardinality` is present and not one of `ALL`, `ONE_OF`,
  `ONE_OR_MORE`. Treat a missing `cardinality` as `ALL`.
- requirements_parser#req-4: fail if two files have the same `title`.
- requirements_parser#req-5: accept a `requirements` list with no entries only if the body is not
  empty, and warn.

## Requirement fields

- requirements_parser#req-6: fail if a requirement has no `id` or no `statement`.
- requirements_parser#req-7: fail if `id` is not a string of decimal digits without a leading zero.
  Accept an unquoted YAML integer and warn that it should be quoted.
- requirements_parser#req-8: fail if a classifier has a value outside its list:

  | Field | Values | Default when missing or empty |
  |---|---|---|
  | `status` | `DRAFT`, `WITHDRAWN`, `CANDIDATE`, `ACCEPTED`, `PUBLISHED`, `DEPRECATED` | `DRAFT` |
  | `level` | `REQUIRED`, `RECOMMENDED` | `RECOMMENDED` (5.3.3) |
  | `type` | `FUNCTIONAL`, `WEBSERVICE`, `INTERFACE` | `FUNCTIONAL` |
  | `observability` | `OBSERVABLE`, `AUDITABLE` | `AUDITABLE` (5.3.4) |
  | `mutability` | `IMMUTABLE`, `EXTENSIBLE`, `REPLACEABLE`, `INAPPLICABLE` | `REPLACEABLE` (5.3.5) |

- requirements_parser#req-9: apply the defaults in req-8 to a missing or empty classifier, and
  report each default applied as a warning, so that the Working Group sees the classifier it did
  not choose.
- requirements_parser#req-10: compare classifier values without regard to case, and warn if a
  value is not in capitals.
- requirements_parser#req-11: fail if `mutability` is `INAPPLICABLE` in a specification that does
  not extend another. *Requirements Model* 5.3.5: it "appears only in an extended specification".
- requirements_parser#req-12: fail if `type` is `WEBSERVICE` and `api_operation_id` is empty.
- requirements_parser#req-13: fail if `api_operation_id` is not the `operationId` of an operation in
  an OpenAPI file under `resources/`.
- requirements_parser#req-14: warn if `observability` is `OBSERVABLE` and `test` is empty.
- requirements_parser#req-15: fail if `test` is not empty and is not a path to a file that exists
  under `resources/`.
- requirements_parser#req-16: warn for every key of a requirement not listed above.

## Statement

The machine-checkable parts of a well-formed requirement statement (Specification Framework,
*Crafting well-formed requirement statements*, section 5).

- requirements_parser#req-17: fail if `statement` spans more than one line or more than one
  sentence.
- requirements_parser#req-18: warn if `statement` holds a term from the list of vague and
  unverifiable terms: `and/or`, `etc.`, `TBD`, `as appropriate`, `if possible`, `user-friendly`,
  `fast`, `easy`, `sufficient`, `minimal`, `adequate`, `some`, `several`, `should be able to`.
  Keep the list in one file of the compiler.
- requirements_parser#req-19: warn if `statement` uses a word from the GovStack obligation
  vocabulary (`MUST`, `SHALL`, `SHOULD`, `MAY`, `REQUIRED`, `OPTIONAL`) whose obligation contradicts
  `level`, for example `MUST` with `level: RECOMMENDED`. *Crafting well-formed requirement
  statements*, 4.3: the classifier is authoritative.
- requirements_parser#req-20: warn if `statement` uses a term that `terminology.yml` lists as
  deprecated.

## Output

- requirements_parser#req-21: pass to the later stages the list of every requirement of the
  specification, each with its `id`, every classifier after defaults, `statement`, the title of its
  functionality, and the file and line it is written in.

## Open

- [ ] *Requirements Model* 5.3.3 lists `DRAFT` and `DEPRECATED` as **level** classifiers. The
  template and *Structure of Specifications* move them to a separate `status` field. req-8 follows
  the template.
- [ ] The template lists `mutability` as `IMMUTABLE`, `EXTENSIBLE`. *Requirements Model* 5.3.5 adds
  `REPLACEABLE` and `INAPPLICABLE`. req-8 follows the model.
- [ ] Link to Key Functionalities (*Requirements Model* 5.3.2): the template has no `kf` field. Is
  the functionality file itself the link, so that `title` is the Key Functionality?
- [ ] How does a specification declare that it extends another, which req-11 needs? *Crafting
  well-formed requirement statements* 5.9 writes `govstack-bb-wallet-cfr#req-1 extends
  govstack-cfr-data#req-3`, but no field carries it.
- [ ] What `WITHDRAWN` means for identifier checks: is a withdrawn requirement kept like a
  deprecated one?
- [ ] Should the default for a missing `status` be `DRAFT`? The Requirements Model gives no default.
