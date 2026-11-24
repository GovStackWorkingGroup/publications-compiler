# Pilot: bb-file-management

`GovStackWorkingGroup/bb-file-management`, branch `v0.3-(new-spec-framework)`, at commit `c545df4`. The branch `main` still has the GitBook layout and is not compiled.

| Field | Value |
|---|---|
| `name` | Document Management |
| `urn` | `govstack:spec:bb:dms` (spec profile) |
| `owner_group` | `govstack:wg:dms` |
| `version` | `0.3-draft`, `latest`, no `release_date` |
| Requirement files | 15: 6 core, 2 in the `collaboration` extension, 7 in the `records` extension |
| Requirements | 147, all `DRAFT`, none with an `id` |

## Expected findings

From a manual dry run on 2026-10-29. The tests of each milestone check that the compiler reports these. Update this list when the pilot branch changes.

### Milestone 1

- Error, `repository_layout#req-2`: `ADR/` and `assets/` are missing.
- Error, `repository_layout#req-6`, `req-7`: `.github/` is missing, so there is no `CODEOWNERS` and no `compile.yml`.
- Error, `toc_parser#req-3`: `resources/reference-products.md` does not exist. The file is `resources/reference-software.md`.
- Warning, `toc_parser#req-5`: `resources/reference-software.md` is not listed.
- Error, `authors_compiler#req-4`: `authors.yml` still holds the template entry `govstack:person:<name>`.
- To decide: `introduction/purpose.md` is listed. See the *Open* of `toc_parser.md`.

### Milestone 2

- Error ×112, `requirements_parser#req-37`: `SERVICE_API` requirements with an empty `api_operation_id`. There is no OpenAPI file under `resources/`.
- Warning ×147, `requirements_parser#req-40`: the statements begin "The DMS", and `name` is "Document Management".
- Warning, `terminology_parser#req-1`: `introduction/terminology.yml` is a list of `entry: {designation, description}`, not `terms: [{term, definition}]`. A warning because the spec profile is not the terminology profile, but no term would render.
- Warning, `repository_layout#req-16`: the snapshot uses the legacy layout, `requirements/`. Its 15 functionalities are read as one core (`conformance_profile_parser#req-1`).
- Warning, `conformance_profile_parser#req-16`: `metadata.yml` has an `extensions` block (`collaboration`, `records`), which is ignored.
- Warning ×15, `conformance_profile_parser#req-17`: every functionality file has `conformance`, and 9 have `extension`. They are ignored, so `requirements_parser#req-32` has no extension to check.
- Warning ×147, `requirements_parser#req-62`: every requirement sets `observability`. For 3 of them it disagrees with the method derived from `interaction`: periodic checksum verification (`archival-preservation.md`), automatic deletion (`document-storage.md`) and rules applied on arrival (`integration-and-events.md`) are `INTERNAL` and `OBSERVABLE`. ARCH-ADR-8 counts a fourth in v0.6, a `SERVICE_API` requirement marked `AUDITABLE`, which v0.3 does not have.
- Warning ×138, `requirements_parser#req-60`: requirements whose `interaction` is not `INTERNAL` and that have no `test`.
- Warning ×4, `requirements_parser#req-64`: `INTERNAL` requirements with an empty `documentation`.
- Passes: every capability is realised, no unknown capability, every statement is one sentence, no `cardinality`, no condition in prose.
- Nothing for `requirement_id_checker.md` to compare: no requirement has an `id`. The requirement index is empty (`renderer#req-24`).

### Milestone 3

- `profiles#req-4`, `req-5`: GovStack Records needs a proposal record for `govstack:spec:bb:dms` and a record for `govstack:wg:dms`.
- Release stops after the checks: `0.3-draft` is a pre-release (`release#req-4`).

## Version

`0.3-draft` parses under `metadata_parser#req-16`, which accepts `MAJOR.MINOR[.PATCH][-SUFFIX]`. It is not a semantic version: Semantic Versioning 2.0.0 requires all three of `MAJOR.MINOR.PATCH`, and a pre-release suffix comes after the patch. `0.3.0-draft` is valid. The suffix is not the problem. D6 decides whether the compiler requires the patch number.

## Later branches

The Working Group has moved on from the pinned snapshot. Branch `v0.6` has `conformance-modules/` pages, a `conformance_modules` block in `metadata.yml` and the functionality files in `requirements/`, a step between the legacy layout and ARCH-ADR-7. Under these requirements it fails `toc_parser#req-2` (`conformance-modules` is not a section) and `repository_layout#req-13` (`conformance-modules/` is not a folder of the layout). ARCH-ADR-7 says the draft moves to `conformance-profiles/`. Re-pin the pilot once it has.

## Second fixture: bb-wallet

`GovStackWorkingGroup/bb-wallet`, branch `feat/v2.0`, at commit `40ccc8c`. It is the first specification in the layout of ARCH-ADR-7, with the variation points and conditions of ARCH-ADR-6, and without `observability` (ARCH-ADR-8). It exercises what the pilot cannot.

| Field | Value |
|---|---|
| `name` | Wallet Building Block Specification |
| `urn` | `govstack:spec:bb:wallet` (spec profile) |
| Conformance profiles | `credential-wallet` (core, 13 functionalities); `verifiable-credentials` (7); `remote-presentation` and `proximity-presentation` (1 each), which require `verifiable-credentials` |
| Variation points | 6, with 18 options and 16 keys; 3 options met by claiming an extension |
| Conditions | 38 `applies_when` and 1 `applies_when_all`, naming option keys and extension ids |
| Requirements with an `id` | 25, from 1.2.0: 13 `PUBLISHED`, 12 `DEPRECATED` |

Expected findings, from a manual dry run on 2026-11-24:

- Passes: exactly one core; `requires` names extensions only, with no cycle; every *Functionalities* table matches its folder (`conformance_profile_parser#req-14`); every profile folder is listed once in `toc.yml`, introduction first; every key and extension id in a condition is defined; every variation point in the core meets `min` without an extension (`requirements_parser#req-51`); no `observability`.
- Error, `toc_parser#req-13`: `resources/research/` does not exist.
- Error, `repository_layout#req-7`: `.github/workflows/compile.yml` is missing.
- Warning, `toc_parser#req-5`: the files of `resources/api/`, `resources/examples/` and `resources/test/` are not listed.
- Warning, `metadata_parser#req-4`: `name` ends with "Building Block Specification". Every statement then fails `requirements_parser#req-40`, which compares the subject with `name`.
- Warning ×3, `requirements_parser#req-16`: `presented_by` on the credential type options (ARCH-ADR-6, question 4).
- Warning ×10, `requirements_parser#req-57`: keys that no condition names: `bio`, `claim`, `cross-device`, `dc-api`, `file`, `nfc`, `qr`, `same-device`, `sd-jwt-vc`, `w3c-vcdm`.
- Warning ×2, `requirements_parser#req-64`: `INTERNAL` requirements with an empty `documentation`.
- Warnings, `requirements_parser#req-9` and `req-60`: most requirements have an empty `mutability` and no `test`, as drafts do.

## To raise with the Working Group

- [ ] The name: the repository says file management, `name` and `url` say document management, the `urn` says `dms`. Confirm before `govstack:spec:bb:dms` is registered.
- [ ] Add `ADR/`, `assets/` and `.github/` from `bb-template`.
- [ ] Fix the `toc.yml` entry for `reference-software.md`.
- [ ] Fill `authors.yml`.
- [ ] Convert `terminology.yml` to the `terms` format.
- [ ] Start an OpenAPI file, or reclassify the requirements whose `interaction` is not an API operation.
