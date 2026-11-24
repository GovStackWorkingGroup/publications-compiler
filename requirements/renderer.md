# Renderer

Renders the book: a static website of one version of a publication. The outputs are set in the GovStack Process, chapter *Compiling Publications*, section *Outputs*.

## When it runs

- renderer#req-1: render only on release, only for a version without a pre-release suffix, and only if no check reported an error (`index#req-8`).
- renderer#req-2: render on any run when given `--render <dir>`, so that a Working Group can preview the book locally. A preview is not deployed.

## The book

- renderer#req-3: write the book of the version to `<output>/<path>/`, where `<path>` is the path of the version URL (`metadata_parser#req-17`). For example, `<output>/bb-wallet/1.2.0/`.
- renderer#req-4: if the version is marked `latest`, also write it to the path of `url`.
- renderer#req-5: title the book with `name` followed by the name of the publication type, read from the profile and the `urn`. For example, `name: Wallet` and `urn: govstack:spec:bb:wallet` give *Wallet Building Block Specification*.
- renderer#req-6: render a front page with the title, `abstract`, the version, its `release_date` and `short_description`, the `urn`, the owner group and the authors with their roles.
- renderer#req-7: render a navigation that follows `toc.yml`: sections, then subsections as headings, then pages, in the order listed.
- renderer#req-36: title the subsection of a conformance profile with the `title` of its introduction, not with its id, and mark the core. In the legacy layout (`repository_layout#req-16`), render the `requirements` section without a subsection.
- renderer#req-8: render a version list from `versions` in `metadata.yml`, with a link to each released version at its version URL.
- renderer#req-9: render every page listed in `toc.yml` and no other page of the repository. The pages the compiler generates, the requirement index (req-43) and the views by key (req-41), are added to these.
- renderer#req-10: copy `assets/` and every file a page links to into the book.
- renderer#req-26: copy `assets/` to `<output>/<path>/assets/`, with `<path>` as in req-3,, and to the path of `url` when req-4 applies, and rewrite every `/assets/<path>` reference (`page_parser#req-16`) to that version's copy. Each version of the book holds its own assets, so an asset changed in a later version does not change an earlier book.
- renderer#req-11: render `mermaid` blocks as diagrams.
- renderer#req-12: render a link to the source of each page in `repository`, at the release tag.

## Special pages

- renderer#req-13: render `terminology.yml` with the layout of *Structure of a Term entry*, *The template*: entry number, preferred term in bold, abbreviation, definition, notes as "Note *n* to entry", and the source in square brackets. Give each entry an anchor from its term.
- renderer#req-14: render `references.yml` as a list per group.
- renderer#req-15: render a folder listed under `resources` as an index page of its files. Render an OpenAPI file in it as an API reference.

## Tags

The tags are parsed by `tag_parser.md` and reach the renderer in canonical form (`tag_parser#req-30`).

- renderer#req-27: render every tag so that its content can be read without JavaScript and when printed. Interaction, such as switching tabs, is an addition, not the only way to reach the content.
- renderer#req-28: render a `hint` as a callout with a label and a colour per `style`: `info` as *Note*, `success` as *Tip*, `warning` as *Caution*, `danger` as *Danger*. In Starlight, an aside of type `note`, `tip`, `caution` or `danger`.
- renderer#req-29: render `tabs` as tabs titled by each `tab`'s `title`, the first one open. When printed, render every tab in order under its title.
- renderer#req-30: render `stepper` as a numbered list of steps, each holding its body.
- renderer#req-31: render `code` as its code block with `title` above it, line numbers when `lineNumbers` is `true`, and wrapped lines when `overflow` is `wrap`.
- renderer#req-32: render `content-ref` as a card with the title of the target and a link to it.
- renderer#req-33: render `file` as a download link with the file name, its type and size, and the caption.
- renderer#req-34: render `embed` as a link to its `url`, with its caption and the host name. Never load content from `url` in the book.
- renderer#req-35: render `openapi` as the operation: method, path, summary, parameters, request body and responses, with the anchor `op-<operationId>`. Link it to every requirement whose `api_operation_id` is that `operationId`, and link those requirements to it.

## Specifications

- ~~renderer#req-16~~: `DEPRECATED`. A functionality belongs to a conformance profile, not to the core or an extension by a field (ARCH-ADR-7), see req-37.
- renderer#req-37: render each functionality file with its `title` and `description`, the titles of the capabilities it realises, the title of its conformance profile with a link to the profile's introduction, and its conditions (req-40); then its requirements, then its body.
- renderer#req-38: render the introduction of each conformance profile with its `title`, whether it is the core or an extension, its `description`, the profiles it `requires` with links to them, its `role` and its `reference_architecture`; then its body.
- renderer#req-17: render each requirement with its `id`, its `statement`, every classifier after defaults, its `interaction` and `contract`, its `documentation`, and links to its API operation and test. Give it the anchor `req-<id>`, unique across the book. The anchor of option `30.2` is `req-30.2`.
- renderer#req-42: render with each requirement its verification method (`requirements_parser#req-61`), *observable* or *auditable*, and mark an *unclassified* one. Do not render `observability` in a specification that has `interaction` (ARCH-ADR-8).
- renderer#req-39: render a variation point with its options indented beneath it, in the order listed, and say how many the solution meets: "at least `min`", and "at most `max`" when it is set. Show the `key` of each option that has one. For an option met by claiming an extension, link to the introduction of that extension profile instead of a test (ARCH-ADR-6).
- renderer#req-40: render the conditions of a conditional requirement next to it: "Applies when" followed by the keys and extension ids of `applies_when`, joined by "or", and of `applies_when_all`, joined by "and". Link each key to its option and each extension id to its profile's introduction. Render the conditions of a functionality once, at its top, and not again on each requirement.
- renderer#req-41: render one page per option `key`: the option that defines it at the top, then every requirement whose conditions name the key, after `requirements_parser#req-54`, grouped by functionality in `toc.yml` order, each linked to where it is written. Say that the page is generated and informative: conformance is assessed per requirement (ARCH-ADR-6, *Views by key*). List the views on the requirement index page.
- renderer#req-18: render a deprecated requirement struck through, with its status shown.
- ~~renderer#req-19~~: `DEPRECATED`. The index gains the conformance profile, the verification method and the conditions, see req-43.
- renderer#req-43: render a requirement index page: one row per numbered requirement, options included, sorted by `id` with each option after its variation point, with its functionality, its conformance profile, the classifiers, `interaction`, the verification method, its conditions, and a link to the requirement.
- ~~renderer#req-20~~: `DEPRECATED`. `extensions` and `conformance` are replaced by conformance profiles, and requirements gain options and conditions, see req-44.
- renderer#req-44: write `requirements.json` at the root of the version's book, with this shape, sorted by `id` with each option after its variation point:

  ```json
  {
    "urn": "govstack:spec:bb:wallet",
    "version": "2.0.0",
    "capabilities": [
      { "id": "credential-collection", "title": "Credential Collection" }
    ],
    "profiles": [
      { "id": "credential-wallet", "title": "Credential Wallet", "type": "CORE", "requires": [] },
      { "id": "verifiable-credentials", "title": "Verifiable Credentials", "type": "EXTENSION", "requires": [] }
    ],
    "functionalities": [
      { "title": "Credential Acquisition", "realises": ["credential-collection"], "profile": "credential-wallet",
        "applies_when": [], "applies_when_all": [] }
    ],
    "requirements": [
      { "id": "30", "status": "PUBLISHED", "level": "REQUIRED", "mutability": "EXTENSIBLE",
        "verification": "options", "select": { "min": 1 },
        "functionality": "Credential Acquisition", "statement": "..." },
      { "id": "30.1", "variation_point": "30", "key": "bearer", "status": "PUBLISHED", "level": "OPTION",
        "interaction": "FILE", "verification": "observable",
        "functionality": "Credential Acquisition", "statement": "..." },
      { "id": "30.3", "variation_point": "30", "key": "vc", "extension": "verifiable-credentials",
        "status": "PUBLISHED", "level": "OPTION", "verification": "extension",
        "functionality": "Credential Acquisition", "statement": "..." },
      { "id": "31", "status": "PUBLISHED", "level": "REQUIRED", "mutability": "EXTENSIBLE",
        "interaction": "FILE", "verification": "observable", "applies_when": ["pass"],
        "functionality": "Credential Acquisition", "statement": "..." }
    ]
  }
  ```

  `applies_when` and `applies_when_all` on a requirement are the conditions that apply to it after `requirements_parser#req-54`.

- renderer#req-21: include in `requirements.json` every field passed by `requirements_parser#req-66` and `conformance_profile_parser#req-18`, so that the next version's checks can read them. The fields in the example above are the minimum.
- renderer#req-22: publish a JSON Schema for `requirements.json` in this repository, and validate the file against it before writing.
- renderer#req-23: include in `requirements.json` every requirement that has an `id`, whatever its status, including withdrawn ones, so that the next version's checks count their numbers. Leave out requirements without an `id`.
- renderer#req-24: render a requirement without an `id` (`DRAFT`, `CANDIDATE`, or `WITHDRAWN` before acceptance) marked as not normative, without a `req-` anchor, and leave it out of the requirement index. A `WITHDRAWN` requirement with an `id` is rendered struck through, as in req-18 (ARCH-ADR-5).
- ~~renderer#req-25~~: `DEPRECATED`. Extensions are conformance profiles, see req-45.
- renderer#req-45: render, on the requirement index page, the capabilities declared in `metadata.yml`, each with its description and the functionalities that realise it, marking a capability realised only in extension profiles; and the conformance profiles, core first, each with its type, description, role, the profiles it requires and its functionalities, linked to its introduction.

## Open

- [ ] The static site generator. It must render CommonMark with tables, Mermaid, and custom pages for requirements and terms. It must not need GitBook syntax.
- [ ] Where the book is deployed, by what, and with which credentials (see `index.md`, *Open*).
- [ ] Does the renderer also produce a PDF of the book?
- [ ] ARCH-ADR-6 does not say where the views by key (req-41) sit in the navigation. req-41 lists them on the requirement index page only.
- [ ] Whether GovMarket and the compliance tooling accept the verification methods `options` and `extension` of a variation point and of an option met by an extension (`requirements_parser#req-61`), or only `observable`, `auditable` and `unclassified`.
