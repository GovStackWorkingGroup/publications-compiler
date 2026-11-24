# Requirements parser

Reads the functionality files of a specification (spec profile). A functionality file describes one functionality. Its requirements are in the YAML front matter, and its body describes the functionality, its workflows and its data structures. A functionality file is a Markdown file in the `functionalities/` folder of a conformance profile (`conformance_profile_parser.md`), or, in the legacy layout, under `requirements/` (`repository_layout#req-16`).

The rules are set in the Specification Framework:

| Chapter | Sets |
|---|---|
| *Structure of Specifications*, *Functionality files* | The file format |
| *Requirements* | What a requirement is, how its statement is written, its identifier, its classifiers, its interaction type, and options and conditions |
| *Functionalities and Conformance Profiles* | Capabilities, and the conformance profiles functionalities belong to |

Some checks follow decisions that are proposed and not yet taken: ARCH-ADR-5 (requirement status and numbering), ARCH-ADR-6 (options and conditional requirements), ARCH-ADR-7 (conformance profiles), ARCH-ADR-8 (verification inferred from `interaction`) and ARCH-TDR-1 (capabilities). Each such check names its decision.

```yaml
---
title: <functionality name>
description: |
   <description from the business point of view>
realises: [] # ids of capabilities declared in metadata.yml
applies_when: [] # optional: keys of options or ids of extension profiles; applies to every requirement in the file
requirements:
  - id: '1'
    status: ACCEPTED # DRAFT, WITHDRAWN, CANDIDATE, ACCEPTED, PUBLISHED, DEPRECATED
    level: RECOMMENDED # RECOMMENDED, REQUIRED; OPTION on options only
    mutability: # IMMUTABLE, EXTENSIBLE, REPLACEABLE, INAPPLICABLE
    interaction: # SERVICE_API, CONSUMED_API, PROTOCOL, EVENT, USER_INTERFACE, DEVICE, FILE, INTERNAL
    contract: # where the contract is defined, when it is defined elsewhere
    applies_when: [] # optional: the requirement applies if the solution meets any of these
    applies_when_all: [] # optional: the requirement applies only if the solution meets all of these
    statement: <one sentence>
    documentation: <Markdown; for an INTERNAL requirement, the evidence a vendor produces>
    api_operation_id:
    test:
  - id: '30' # a variation point: no interaction, api_operation_id or test
    status: ACCEPTED
    level: REQUIRED
    mutability: EXTENSIBLE
    statement: <one sentence naming the choice>
    select:
      min: 1 # default 1
      max: # optional, default no maximum
    options:
      - id: '30.1'
        key: pass # optional: only when another requirement names it
        status: ACCEPTED
        level: OPTION
        interaction: FILE
        statement: <one sentence>
        test:
      - id: '30.2'
        key: vc
        status: ACCEPTED
        level: OPTION
        extension: verifiable-credentials # met by claiming this extension profile: no interaction or test
        statement: <one sentence>
---
```

## Functionality fields

- requirements_parser#req-1: fail if a functionality file has no front matter, no `title`, or no `requirements` list.
- ~~requirements_parser#req-2~~: `DEPRECATED`. `capability` is renamed `description`, see req-22.
- ~~requirements_parser#req-3~~: `DEPRECATED`. `cardinality` is replaced by `select` on a variation point (ARCH-ADR-6), see req-41.
- requirements_parser#req-4: fail if two functionality files have the same `title`, in the same conformance profile or in different ones.
- requirements_parser#req-5: accept a `requirements` list with no entries only if the body is not empty, and warn.
- requirements_parser#req-22: warn if `description` is missing or empty. If the file has `capability` and no `description`, read `capability` as `description`, and warn that the field is renamed (ARCH-TDR-1).
- requirements_parser#req-41: warn if a functionality file has `cardinality`, and do not read it. The message says that a choice between requirements is a variation point with `select` (ARCH-ADR-6), and that `ONE_OF` becomes `min: 1, max: 1` and `ONE_OR_MORE` becomes `min: 1`.

### Capabilities

A functionality names the capabilities it realises. Capabilities are declared in `metadata.yml` (`metadata_parser#req-24`). *Functionalities and Conformance Profiles*, *Capabilities and functionalities*, rules 1 and 2.

- requirements_parser#req-23: fail if `realises` is missing, empty, or not a list of strings.
- requirements_parser#req-24: fail for every id in `realises` that is not declared under `capabilities` in `metadata.yml`.
- requirements_parser#req-25: fail for every capability declared in `metadata.yml` that no functionality realises.
- requirements_parser#req-26: if `metadata.yml` declares no capabilities, skip req-23 to req-25 and warn once, so that specifications written before ARCH-TDR-1 stay valid.

### Conformance profiles

A functionality belongs to the conformance profile whose `functionalities/` folder holds its file (ARCH-ADR-7). It carries no field that says so. The profiles are read by `conformance_profile_parser.md`, which also warns about the `conformance` and `extension` fields of the earlier model (`conformance_profile_parser#req-17`).

- ~~requirements_parser#req-27~~: `DEPRECATED`. `conformance` is no longer read (ARCH-ADR-7), see `conformance_profile_parser#req-17`.
- ~~requirements_parser#req-28~~: `DEPRECATED`. As req-27.
- ~~requirements_parser#req-29~~: `DEPRECATED`. `extension` on a functionality is no longer read (ARCH-ADR-7), see `conformance_profile_parser#req-17`.
- ~~requirements_parser#req-30~~: `DEPRECATED`. The core is a profile folder with at least one functionality, see `conformance_profile_parser#req-4` and `req-8`.
- ~~requirements_parser#req-31~~: `DEPRECATED`. An extension is a profile folder with at least one functionality, see `conformance_profile_parser#req-4`.
- requirements_parser#req-32: warn for every extension profile none of whose requirements is `REQUIRED`. Claiming it guarantees nothing, and its requirements may be better written as `RECOMMENDED` requirements in the core.

## Requirement fields

- ~~requirements_parser#req-6~~: `DEPRECATED`. A requirement may have no `id` while it is unnumbered, see req-33 and req-34.
- requirements_parser#req-33: fail if a requirement has no `statement`.
- requirements_parser#req-34: check `id` against `status` (*Requirements*, *Requirement status*; ARCH-ADR-5). Fail if a `DRAFT` or `CANDIDATE` requirement has an `id`, and if an `ACCEPTED`, `PUBLISHED` or `DEPRECATED` requirement has none. A `WITHDRAWN` requirement may have either. This applies to options, each with its own `status`.
- requirements_parser#req-7: when `id` is set on a requirement that is not an option, fail if it is not a string of decimal digits without a leading zero. Accept an unquoted YAML integer and warn that it should be quoted. The `id` of an option is checked by req-48.
- requirements_parser#req-8: fail if a classifier has a value outside its list. The lists and defaults are set in *Requirements*, *Classifiers*:

  | Field | Values | Default when missing or empty |
  |---|---|---|
  | `status` | `DRAFT`, `WITHDRAWN`, `CANDIDATE`, `ACCEPTED`, `PUBLISHED`, `DEPRECATED` | `DRAFT` |
  | `level` | `REQUIRED`, `RECOMMENDED`, `OPTION` (ARCH-ADR-6, options only, see req-46) | `RECOMMENDED`; `OPTION` for an option |
  | `observability` | `OBSERVABLE`, `AUDITABLE`; read only under req-63 (ARCH-ADR-8) | `AUDITABLE` |
  | `mutability` | `IMMUTABLE`, `EXTENSIBLE`, `REPLACEABLE`, `INAPPLICABLE` | `REPLACEABLE` |
  | `interaction` | `SERVICE_API`, `CONSUMED_API`, `PROTOCOL`, `EVENT`, `USER_INTERFACE`, `DEVICE`, `FILE`, `INTERNAL` | none, see req-35 |

- requirements_parser#req-9: apply the defaults in req-8 to a missing or empty classifier, and report each default applied as a warning, so that the Working Group sees the classifier it did not choose.
- requirements_parser#req-10: compare classifier values without regard to case, and warn if a value is not in capitals.
- requirements_parser#req-11: fail if `mutability` is `INAPPLICABLE` in a specification that does not extend another. *Requirements*, *Mutability*: it is "used only in an extending specification".
- requirements_parser#req-35: warn if `interaction` is missing or empty. Apply no default: the interaction type says where a tester has to stand, and a guessed one would mislead. Skip this check for a variation point (req-45) and for an option met by claiming an extension (req-50).
- requirements_parser#req-36: if a requirement has the field `type`, warn that it is replaced by `interaction`, and do not read it as `interaction`. The message suggests `SERVICE_API` for `WEBSERVICE`, `INTERNAL` for `FUNCTIONAL`, and one of the other boundary types for `INTERFACE`.
- ~~requirements_parser#req-12~~: `DEPRECATED`. `type` is replaced by `interaction`, see req-37.
- requirements_parser#req-37: fail if `interaction` is `SERVICE_API` and `api_operation_id` is empty. *Requirements*, *Interaction types*, rule 2.
- requirements_parser#req-38: warn if `api_operation_id` is set and `interaction` is not `SERVICE_API`.
- requirements_parser#req-39: warn if `interaction` is `CONSUMED_API`, `PROTOCOL`, `DEVICE` or `FILE` and `contract` is empty. The message says to name the contract, or to ignore the warning if this specification defines it. *Requirements*, *Interaction types*, rule 3.
- requirements_parser#req-13: fail if `api_operation_id` is not the `operationId` of an operation in an OpenAPI file under `resources/`.
- ~~requirements_parser#req-14~~: `DEPRECATED`. Whether a requirement needs a test follows from `interaction` (ARCH-ADR-8), see req-60; for a specification without `interaction`, req-63.
- requirements_parser#req-15: fail if `test` is not empty and is not a path to a file that exists under `resources/`.
- requirements_parser#req-16: warn for every key of a requirement not listed in req-8 or in the template above.

## Verification

How a requirement is verified follows from its `interaction` (ARCH-ADR-8). A requirement whose `interaction` crosses a boundary is observed at that boundary. An `INTERNAL` requirement cannot be seen at any boundary and is audited against the evidence its `documentation` lists. Building Block specifications no longer set `observability`. The cross-functional requirements (`govstack:spec:cfr`) carry no `interaction` and keep it.

- requirements_parser#req-61: derive the verification method of every requirement: `observable` if `interaction` is any type other than `INTERNAL`, `auditable` if it is `INTERNAL`, and `unclassified` if it is missing. A variation point is verified by `options`, by counting the options that pass (ARCH-ADR-6, rule 2). An option met by claiming an extension is verified by `extension`, through the extension's tests. Apply no default to an unclassified requirement: it is reported as unclassified, not as auditable.
- requirements_parser#req-60: warn if the verification method is `observable` and `test` is empty.
- requirements_parser#req-64: warn if `interaction` is `INTERNAL` and `documentation` is empty. The message says that `documentation` lists the evidence a vendor produces for an audit.
- requirements_parser#req-62: warn for every requirement that has `observability`, and do not read it. The message says that the verification method follows from `interaction`. When `observability` disagrees with the derived method (`OBSERVABLE` on an `INTERNAL` requirement, or `AUDITABLE` on any other type), name both, and say that the Working Group corrects the `interaction` if it is the `observability` that was right.
- requirements_parser#req-63: if no requirement of the specification has `interaction`, as in the cross-functional requirements, read `observability` as req-8 and req-9 say, take the verification method from it, skip req-60 to req-62, and warn if `observability` is `OBSERVABLE` and `test` is empty.

## Variation points and options

A variation point is a requirement that a solution meets by meeting at least a number of its options. It cannot be skipped. Its options are listed beneath it, each a requirement of level `OPTION` (ARCH-ADR-6; *Requirements*, *Options and conditions*).

- requirements_parser#req-42: read a requirement that has `select` as a variation point, and every entry of its `options` list as a requirement nested in it, checked by every rule of this file. Fail if a requirement has `options` and no `select`, or `select` and no `options`.
- requirements_parser#req-43: fail if a variation point has fewer than two options. A choice that a solution may skip entirely is not a variation point: it is a set of `RECOMMENDED` requirements or an extension profile.
- requirements_parser#req-44: fail if `select` is not a map, if `min` is set and is not an integer of at least 1 and at most the number of options, or if `max` is set and is not an integer of at least `min` and at most the number of options. Treat a missing `min` as 1. Warn for any other key of `select`.
- requirements_parser#req-45: fail if a variation point has `interaction`, `api_operation_id` or `test`. It states the choice and nothing else, and is verified by counting the options that pass (rule 2).
- requirements_parser#req-46: fail if a requirement that is not in the `options` list of a variation point has level `OPTION`, and if an option has a level other than `OPTION`.
- requirements_parser#req-47: fail if an option has `select` or `options`. Choices that follow from an option are separate variation points with `applies_when` (rule 5).
- requirements_parser#req-48: when an option has an `id`, fail unless its variation point has an `id` and the option's `id` is that `id`, a full stop and a decimal number without a leading zero, such as `30.2`. Fail if two options of one variation point have the same number.
- requirements_parser#req-49: when an option has a `key`, fail if the key uses anything but lowercase letters, digits and hyphens, or if it is the key of another option or the id of a conformance profile of the specification.
- requirements_parser#req-50: when an option has `extension`, fail if it is not the id of an extension profile (`conformance_profile_parser.md`), and fail if the option has `interaction`, `api_operation_id` or `test`: the extension's tests are its test. An option without `extension` is checked by req-35 and req-60 like any other requirement.
- requirements_parser#req-51: fail for every variation point in a functionality of the core of which fewer than `min` options have no `extension`. The core must be met without any extension (rule 4).
- requirements_parser#req-52: warn for every `PUBLISHED` `REQUIRED` variation point with fewer than `min` `PUBLISHED` options. Only published options count towards `min`, so no solution can conform. A variation point that is not yet published takes no part in conformance and is not checked.
- requirements_parser#req-65: skip req-19 for options. An option does not bind on its own, so an obligation word in its statement cannot be compared with its level.
- requirements_parser#req-58: warn if a requirement or a functionality has `option_of`, and do not read it. The message says that an option is listed under `options` of its variation point.

## Conditions

A conditional requirement applies only when the solution meets a given option or claims a given extension profile. It is reported as not applicable otherwise (ARCH-ADR-6; ARCH-ADR-7, rule 6).

- requirements_parser#req-53: fail if `applies_when` or `applies_when_all`, on a requirement or in the front matter of a functionality, is not a list of strings, and for every entry that is neither the `key` of an option nor the id of an extension profile of the specification. The core cannot be a condition: every solution meets it.
- requirements_parser#req-54: apply the `applies_when` and `applies_when_all` of a functionality to every requirement in its file. A requirement with conditions of its own applies only when both its own conditions and the functionality's hold.
- requirements_parser#req-55: fail if a requirement is conditional on itself: on the key of one of its own options, or, through the variation points the condition names and their own conditions, on a key that leads back to it.
- requirements_parser#req-56: warn if `statement` begins with "When" or "If" and the requirement has no condition, of its own or from its functionality. The message says to move the condition into `applies_when` and write the statement without it (rule 7).
- requirements_parser#req-57: warn for every `key` that no `applies_when` or `applies_when_all` in the specification names. An option that nothing refers to needs no key.

## Statement

The machine-checkable parts of a well-formed requirement statement (*Requirements*, *Writing the statement*). They apply to variation points and options as to any requirement.

- requirements_parser#req-17: fail if `statement` spans more than one line or more than one sentence.
- requirements_parser#req-18: warn if `statement` holds a term from the list of vague and unverifiable terms: `and/or`, `etc.`, `TBD`, `as appropriate`, `if possible`, `user-friendly`, `user friendly`, `fast`, `easy`, `sufficient`, `minimal`, `adequate`, `some`, `several`, `should be able to`, `as a minimum`, `provide support for`, `best`, `better than`. Keep the list in one file of the compiler. *Requirements*, *Words to avoid*.
- requirements_parser#req-19: warn if `statement` uses a word from the GovStack obligation vocabulary (`MUST`, `SHALL`, `SHOULD`, `MAY`, `REQUIRED`, `OPTIONAL`) whose obligation contradicts `level`, for example `MUST` with `level: RECOMMENDED`. *Requirements*, *Obligation*: the classifier is authoritative.
- requirements_parser#req-20: warn if `statement` uses a term that `terminology.yml` lists as deprecated.
- requirements_parser#req-40: warn if the subject of `statement` is not the Building Block: if the statement, after an optional leading condition that ends in a comma, does not begin with "The" followed by `name` from `metadata.yml`, compared without regard to case. *Requirements*, *Writing the statement*, rule 1, and *Common mistakes*, "The wrong subject".

## Output

- ~~requirements_parser#req-21~~: `DEPRECATED`. Functionalities no longer carry `conformance` and `extension`, see req-66.
- requirements_parser#req-66: pass to the later stages the list of every functionality, with its `title`, `description`, `realises`, the id of its conformance profile, and its `applies_when` and `applies_when_all`; and the list of every requirement of the specification, each with its `id` if it has one, every classifier after defaults, `interaction`, its verification method (req-61), `contract`, `applies_when` and `applies_when_all` as written and as they apply after req-54, `statement`, the title of its functionality, and the file and line it is written in. A variation point also carries `select` and its options in order. An option also carries the `id` of its variation point, its `key` and its `extension`.

## Open

- [ ] *Structure of Specifications* and `bb-template` list `mutability` as `IMMUTABLE`, `EXTENSIBLE`. *Requirements*, *Mutability*, adds `REPLACEABLE` and `INAPPLICABLE`. req-8 follows *Requirements*.
- [ ] How does a specification declare that it extends another, which req-11 needs? *Requirements*, *Mutability*, writes `govstack:spec:bb:wallet#req-1 extends govstack:spec:cfr:data#req-3`, but no field carries it.
- [ ] *Requirements* asks whether `interaction` becomes required on every requirement. If it does, req-35 becomes an error. ARCH-ADR-8 makes the case stronger: without `interaction`, a requirement has no verification method.
- [ ] The format of `contract` is open (*Requirements*, question 4). Once it is a publication identifier with a defined anchor syntax, the compiler can check it.
- [ ] *Introductory Chapters*, *Interfaces*: every interface is realised by a requirement of the matching `interaction`, and every requirement that is not `INTERNAL` realises a listed interface. Under ARCH-ADR-7 the *Interfaces* table moves to the introduction of each conformance profile. Checking this needs the compiler to read that table, which is free Markdown today.
- [ ] req-40 assumes the Building Block is named by `name` in `metadata.yml`, such as "Wallet". A specification whose statements use another form, such as "The Wallet Building Block", gets no warning; one that uses an abbreviation gets one.
- [ ] ARCH-ADR-6 and ARCH-ADR-7 are proposed, and ARCH-ADR-6 still writes `conformance`, `extension` and `observability` in its examples and checks that an option's `extension` is "declared in `metadata.yml`". This file reads ARCH-ADR-6 in the layout of ARCH-ADR-7 and without `observability` (ARCH-ADR-8). The Architecture Working Group agrees the merge order on 2026-11-25.
- [ ] ARCH-ADR-6, rule 2, sets the `observability` of a variation point from its options. Under ARCH-ADR-8 a variation point has no `interaction`, so req-61 gives it the method `options` instead. GovMarket needs to accept that value, or the two ADRs need to agree another one.
- [ ] ARCH-ADR-6, question 3: whether an option added later to an accepted variation point takes its `n.m` number when it is added, or only when it is accepted. req-34 and req-48 apply the second reading, as for every requirement.
- [ ] ARCH-ADR-6, question 4: whether an option may name the options of another variation point it works with. The Wallet writes `presented_by` on its credential type options. Until the question is settled, req-16 warns about it, and the compiler does not check that the names are options.
- [ ] ARCH-ADR-6, question 6: whether `select` keeps `max`. req-44 accepts it.
- [ ] ARCH-ADR-6 does not say how the conditions of a functionality and of one of its requirements combine. req-54 requires both to hold.
- [ ] Whether the statement of an option uses an obligation word. ARCH-ADR-6 writes options without one ("The Wallet adds a pass…"), and req-65 does not check it.
- [ ] req-56 recognises a condition in prose by its first word only. "Where", "Once" and "Unless" are not caught.
