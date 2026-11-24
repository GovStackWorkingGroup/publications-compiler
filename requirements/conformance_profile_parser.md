# Conformance profile parser

Reads the conformance profiles of a specification (spec profile). A conformance profile is a named set of functionalities that a software solution claims as a whole. A specification has one core, which every conformant software solution meets, and any number of extensions, which a software solution may claim in addition. The term is always written in full, so that it is not confused with a publication profile (`profiles.md`).

The rules are set in the Specification Framework, *Functionalities and Conformance Profiles* (`chapters/functionalities_and_extensions.md`) and *Structure of Specifications*, *Conformance profile files*. They follow ARCH-ADR-7, which is proposed and not yet taken.

```text
conformance-profiles/
├── credential-wallet/         the folder name is the id of the profile
│   ├── introduction.md        declares and describes the profile
│   └── functionalities/
│       ├── secure-storage.md  one functionality file each, read by requirements_parser.md
│       └── portability.md
└── verifiable-credentials/
    ├── introduction.md
    └── functionalities/
        └── vc-acquisition.md
```

```yaml
---
title: Verifiable Credentials
type: EXTENSION # CORE, EXTENSION
description: <what a software solution that claims it can do that the core alone cannot>
requires: [] # ids of other extension profiles this one depends on
role: Credential Holder, towards the Credential Issuer # optional
reference_architecture: govstack:ra:credentials # optional
---
```

## Folders

- conformance_profile_parser#req-1: in the spec profile, run after `toc_parser.md` and before `requirements_parser.md`, when `conformance-profiles/` exists. A specification in the legacy layout (`repository_layout#req-16`) has one implicit profile of type `CORE`, with the id `core`, the `name` of `metadata.yml` as title, and every functionality file under `requirements/`; req-2 to req-15 are skipped for it.
- conformance_profile_parser#req-2: fail for every file directly under `conformance-profiles/`. It holds only folders, one per profile.
- conformance_profile_parser#req-3: fail for every profile folder whose name is not `kebab-case` (`repository_layout.md`, *File and folder names*). The folder name is the id of the profile, cited in conformance declarations, and names the profile, never its type.
- conformance_profile_parser#req-4: fail for every profile folder that has no `introduction.md`, no `functionalities/` folder, or no Markdown file in `functionalities/`.
- conformance_profile_parser#req-5: warn for every file or folder in a profile folder other than `introduction.md` and `functionalities/`, and for every folder inside `functionalities/`. A functionality belongs to the profile whose `functionalities/` folder holds its file, and to no other.

## Declaration

- conformance_profile_parser#req-6: fail if the front matter of `introduction.md` is missing, or `title`, `type` or `description` is missing or empty. Warn for every key other than `title`, `type`, `description`, `requires`, `role` and `reference_architecture`.
- conformance_profile_parser#req-7: fail if `type` is not `CORE` or `EXTENSION`. Compare without regard to case, and warn if the value is not in capitals.
- conformance_profile_parser#req-8: fail unless exactly one profile has `type: CORE`.
- conformance_profile_parser#req-9: fail if two profiles have the same `title`.
- conformance_profile_parser#req-10: fail if `role` is set and is not a string.
- conformance_profile_parser#req-11: fail if `reference_architecture` is set and is not a `govstack:ra:<name>` identifier. This replaces `metadata_parser#req-28`.
- conformance_profile_parser#req-12: treat a missing `requires` as empty. Fail if `requires` is not a list, if it is not empty in the core, or if it names an id that is not the folder of an extension profile, names the profile itself, or forms a cycle between profiles. The core never depends on an extension (rule 5).

## Introduction

The body of `introduction.md` describes the profile for a buyer and an assessor (*Functionalities and Conformance Profiles*, *Writing the introduction of a profile*).

- conformance_profile_parser#req-13: warn if the body has no level-2 heading *Why choose this conformance profile?*, *Example use cases* or *Functionalities*.
- conformance_profile_parser#req-14: read the links in the first table after the heading *Functionalities*. Fail for every link that does not resolve to a file in the `functionalities/` folder of the same profile, and for every file in that folder that the table does not link to. The table may not disagree with where the files are.
- conformance_profile_parser#req-15: warn if *Functionalities* has no table, and skip req-14.

## Fields that no longer apply

- conformance_profile_parser#req-16: warn if `metadata.yml` has an `extensions` block, and ignore it. The message says that each extension is declared in the front matter of its `introduction.md`. This replaces `metadata_parser#req-26` to `req-29`.
- conformance_profile_parser#req-17: warn for every functionality file that has a `conformance` or `extension` field in its front matter, and ignore the field. The message says that the profile of a functionality is the folder its file is in. This replaces `requirements_parser#req-27` to `req-29`.

## Output

- conformance_profile_parser#req-18: pass to the later stages the list of profiles, core first and then in `toc.yml` order, each with its id, `title`, `type`, `description`, `requires`, `role`, `reference_architecture`, the path of its introduction, and the paths of its functionality files.

## Open

- [ ] ARCH-ADR-7 is proposed. The Architecture Working Group discusses it on 2026-11-25: one core plus extensions as profile types, the folder name as a permanent id, and the `toc.yml` subsections under `conformance-profiles`.
- [ ] The id of the implicit profile of the legacy layout (req-1). ARCH-ADR-7 says a published specification "has a single profile" before it moves, but not what it is called. When the specification moves, its core takes the id of its folder, and `requirement_id_checker#req-21` lets `core` go. Is `core` right, or should the legacy layout have no profile id?
- [ ] How an extension is deprecated. ARCH-ADR-7, *Versioning*, makes deprecating an extension major, but defines no field for it. Until it does, a profile folder that disappears fails `requirement_id_checker#req-21`.
- [ ] ARCH-ADR-6, question 5: whether `requires` may name option keys as well as extension ids. The Wallet's `proximity-presentation` needs the `mdoc` option of `verifiable-credentials`. req-12 accepts extension ids only.
- [ ] ARCH-ADR-6, question 2: whether an extension's declaration names the option it meets. If it does, req-6 accepts the key and `requirements_parser#req-50` checks it from both sides.
- [ ] The namespaces chapter of the GovStack Process defines how a profile is cited, for example the identifier of the specification plus the folder name. The compiler does not render a citation until it is defined.
