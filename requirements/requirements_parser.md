# Requirements parser

Reads the requirement files of a specification (spec profile). A requirement file describes one functionality. Its requirements are in the YAML front matter, and its body describes the functionality, its workflows and its data structures.

The rules are set in the Specification Framework:

| Chapter | Sets |
|---|---|
| *Structure of Specifications*, *Requirement files* | The file format |
| *Requirements* | What a requirement is, how its statement is written, its identifier, its classifiers and its interaction type |
| *Functionalities and Extensions* | Capabilities, and core and extension functionalities |

Some checks follow decisions that are proposed and not yet taken: ARCH-ADR-5 (requirement status and numbering), ARCH-ADR-7 (core and extensions) and ARCH-TDR-1 (capabilities). Each such check names its decision.

```yaml
---
title: <functionality name>
description: |
   <description from the business point of view>
realises: [] # ids of capabilities declared in metadata.yml
conformance: CORE # CORE, EXTENSION
extension: # id of an extension declared in metadata.yml; only when conformance is EXTENSION
cardinality: ALL # ALL, ONE_OF, ONE_OR_MORE
requirements:
  - id: '1'
    status: ACCEPTED # DRAFT, WITHDRAWN, CANDIDATE, ACCEPTED, PUBLISHED, DEPRECATED
    level: RECOMMENDED # RECOMMENDED, REQUIRED
    observability: # OBSERVABLE, AUDITABLE
    mutability: # IMMUTABLE, EXTENSIBLE, REPLACEABLE, INAPPLICABLE
    interaction: # SERVICE_API, CONSUMED_API, PROTOCOL, EVENT, USER_INTERFACE, DEVICE, FILE, INTERNAL
    contract: # where the contract is defined, when it is defined elsewhere
    statement: <one sentence>
    documentation: <Markdown>
    api_operation_id:
    test:
---
```

## Functionality fields

- requirements_parser#req-1: fail if a file listed under `requirements` in `toc.yml` has no front matter, no `title`, or no `requirements` list.
- ~~requirements_parser#req-2~~: `DEPRECATED`. `capability` is renamed `description`, see req-22.
- requirements_parser#req-3: fail if `cardinality` is present and not one of `ALL`, `ONE_OF`, `ONE_OR_MORE`. Treat a missing `cardinality` as `ALL`.
- requirements_parser#req-4: fail if two files have the same `title`.
- requirements_parser#req-5: accept a `requirements` list with no entries only if the body is not empty, and warn.
- requirements_parser#req-22: warn if `description` is missing or empty. If the file has `capability` and no `description`, read `capability` as `description`, and warn that the field is renamed (ARCH-TDR-1).

### Capabilities

A functionality names the capabilities it realises. Capabilities are declared in `metadata.yml` (`metadata_parser#req-24`). *Functionalities and Extensions*, *Capabilities and functionalities*, rules 1 and 2.

- requirements_parser#req-23: fail if `realises` is missing, empty, or not a list of strings.
- requirements_parser#req-24: fail for every id in `realises` that is not declared under `capabilities` in `metadata.yml`.
- requirements_parser#req-25: fail for every capability declared in `metadata.yml` that no functionality realises.
- requirements_parser#req-26: if `metadata.yml` declares no capabilities, skip req-23 to req-25 and warn once, so that specifications written before ARCH-TDR-1 stay valid.

### Core and extensions

A functionality is either in the core or in exactly one extension. Extensions are declared in `metadata.yml` (`metadata_parser#req-26`). *Functionalities and Extensions*, *Core and extension functionalities* (ARCH-ADR-7).

- requirements_parser#req-27: fail if `conformance` is present and not `CORE` or `EXTENSION`. Treat a missing `conformance` as `CORE`, without a warning, so that specifications written before ARCH-ADR-7 stay valid.
- requirements_parser#req-28: fail if `conformance` is `EXTENSION` and `extension` is empty, or if `conformance` is `CORE` and `extension` is set.
- requirements_parser#req-29: fail if `extension` is not the id of an extension declared in `metadata.yml`.
- requirements_parser#req-30: fail if no functionality is `CORE`.
- requirements_parser#req-31: fail for every extension declared in `metadata.yml` that no functionality belongs to.
- requirements_parser#req-32: warn for every extension none of whose requirements is `REQUIRED`. Claiming it guarantees nothing, and its requirements may be better written as `RECOMMENDED` requirements in the core.

## Requirement fields

- ~~requirements_parser#req-6~~: `DEPRECATED`. A requirement may have no `id` while it is unnumbered, see req-33 and req-34.
- requirements_parser#req-33: fail if a requirement has no `statement`.
- requirements_parser#req-34: check `id` against `status` (*Requirements*, *Requirement status*; ARCH-ADR-5). Fail if a `DRAFT` or `CANDIDATE` requirement has an `id`, and if an `ACCEPTED`, `PUBLISHED` or `DEPRECATED` requirement has none. A `WITHDRAWN` requirement may have either.
- requirements_parser#req-7: when `id` is set, fail if it is not a string of decimal digits without a leading zero. Accept an unquoted YAML integer and warn that it should be quoted.
- requirements_parser#req-8: fail if a classifier has a value outside its list. The lists and defaults are set in *Requirements*, *Classifiers*:

  | Field | Values | Default when missing or empty |
  |---|---|---|
  | `status` | `DRAFT`, `WITHDRAWN`, `CANDIDATE`, `ACCEPTED`, `PUBLISHED`, `DEPRECATED` | `DRAFT` |
  | `level` | `REQUIRED`, `RECOMMENDED` | `RECOMMENDED` |
  | `observability` | `OBSERVABLE`, `AUDITABLE` | `AUDITABLE` |
  | `mutability` | `IMMUTABLE`, `EXTENSIBLE`, `REPLACEABLE`, `INAPPLICABLE` | `REPLACEABLE` |
  | `interaction` | `SERVICE_API`, `CONSUMED_API`, `PROTOCOL`, `EVENT`, `USER_INTERFACE`, `DEVICE`, `FILE`, `INTERNAL` | none, see req-35 |

- requirements_parser#req-9: apply the defaults in req-8 to a missing or empty classifier, and report each default applied as a warning, so that the Working Group sees the classifier it did not choose.
- requirements_parser#req-10: compare classifier values without regard to case, and warn if a value is not in capitals.
- requirements_parser#req-11: fail if `mutability` is `INAPPLICABLE` in a specification that does not extend another. *Requirements*, *Mutability*: it is "used only in an extending specification".
- requirements_parser#req-35: warn if `interaction` is missing or empty. Apply no default: the interaction type says where a tester has to stand, and a guessed one would mislead.
- requirements_parser#req-36: if a requirement has the field `type`, warn that it is replaced by `interaction`, and do not read it as `interaction`. The message suggests `SERVICE_API` for `WEBSERVICE`, `INTERNAL` for `FUNCTIONAL`, and one of the other boundary types for `INTERFACE`.
- ~~requirements_parser#req-12~~: `DEPRECATED`. `type` is replaced by `interaction`, see req-37.
- requirements_parser#req-37: fail if `interaction` is `SERVICE_API` and `api_operation_id` is empty. *Requirements*, *Interaction types*, rule 2.
- requirements_parser#req-38: warn if `api_operation_id` is set and `interaction` is not `SERVICE_API`.
- requirements_parser#req-39: warn if `interaction` is `CONSUMED_API`, `PROTOCOL`, `DEVICE` or `FILE` and `contract` is empty. The message says to name the contract, or to ignore the warning if this specification defines it. *Requirements*, *Interaction types*, rule 3.
- requirements_parser#req-13: fail if `api_operation_id` is not the `operationId` of an operation in an OpenAPI file under `resources/`.
- requirements_parser#req-14: warn if `observability` is `OBSERVABLE` and `test` is empty.
- requirements_parser#req-15: fail if `test` is not empty and is not a path to a file that exists under `resources/`.
- requirements_parser#req-16: warn for every key of a requirement not listed in req-8 or in the template above.

## Statement

The machine-checkable parts of a well-formed requirement statement (*Requirements*, *Writing the statement*).

- requirements_parser#req-17: fail if `statement` spans more than one line or more than one sentence.
- requirements_parser#req-18: warn if `statement` holds a term from the list of vague and unverifiable terms: `and/or`, `etc.`, `TBD`, `as appropriate`, `if possible`, `user-friendly`, `user friendly`, `fast`, `easy`, `sufficient`, `minimal`, `adequate`, `some`, `several`, `should be able to`, `as a minimum`, `provide support for`, `best`, `better than`. Keep the list in one file of the compiler. *Requirements*, *Words to avoid*.
- requirements_parser#req-19: warn if `statement` uses a word from the GovStack obligation vocabulary (`MUST`, `SHALL`, `SHOULD`, `MAY`, `REQUIRED`, `OPTIONAL`) whose obligation contradicts `level`, for example `MUST` with `level: RECOMMENDED`. *Requirements*, *Obligation*: the classifier is authoritative.
- requirements_parser#req-20: warn if `statement` uses a term that `terminology.yml` lists as deprecated.
- requirements_parser#req-40: warn if the subject of `statement` is not the Building Block: if the statement, after an optional leading condition that ends in a comma, does not begin with "The" followed by `name` from `metadata.yml`, compared without regard to case. *Requirements*, *Writing the statement*, rule 1, and *Common mistakes*, "The wrong subject".

## Output

- requirements_parser#req-21: pass to the later stages the list of every functionality, with its `title`, `description`, `realises`, `conformance` and `extension`; and the list of every requirement of the specification, each with its `id` if it has one, every classifier after defaults, `interaction`, `contract`, `statement`, the title of its functionality, and the file and line it is written in.

## Open

- [ ] *Structure of Specifications* and `bb-template` list `mutability` as `IMMUTABLE`, `EXTENSIBLE`. *Requirements*, *Mutability*, adds `REPLACEABLE` and `INAPPLICABLE`. req-8 follows *Requirements*.
- [ ] How does a specification declare that it extends another, which req-11 needs? *Requirements*, *Mutability*, writes `govstack:spec:bb:wallet#req-1 extends govstack:spec:cfr:data#req-3`, but no field carries it.
- [ ] *Requirements* asks whether `interaction` becomes required on every requirement. If it does, req-35 becomes an error.
- [ ] The format of `contract` is open (*Requirements*, question 4). Once it is a publication identifier with a defined anchor syntax, the compiler can check it.
- [ ] *Introductory Chapters*, *Interfaces*: every interface is realised by a requirement of the matching `interaction`, and every requirement that is not `INTERNAL` realises a listed interface. Checking this needs the compiler to read the *Interfaces* table of `introduction/overview.md`, which is free Markdown today.
- [ ] req-40 assumes the Building Block is named by `name` in `metadata.yml`, such as "Wallet". A specification whose statements use another form, such as "The Wallet Building Block", gets no warning; one that uses an abbreviation gets one.
