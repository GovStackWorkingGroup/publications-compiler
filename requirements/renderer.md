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
- renderer#req-8: render a version list from `versions` in `metadata.yml`, with a link to each released version at its version URL.
- renderer#req-9: render every page listed in `toc.yml` and no other page.
- renderer#req-10: copy `assets/` and every file a page links to into the book.
- renderer#req-26: copy `assets/` to `<output>/<path>/assets/`, with `<path>` as in req-3,, and to the path of `url` when req-4 applies, and rewrite every `/assets/<path>` reference (`page_parser#req-16`) to that version's copy. Each version of the book holds its own assets, so an asset changed in a later version does not change an earlier book.
- renderer#req-11: render `mermaid` blocks as diagrams.
- renderer#req-12: render a link to the source of each page in `repository`, at the release tag.

## Special pages

- renderer#req-13: render `terminology.yml` with the layout of *Structure of a Term entry*, *The template*: entry number, preferred term in bold, abbreviation, definition, notes as "Note *n* to entry", and the source in square brackets. Give each entry an anchor from its term.
- renderer#req-14: render `references.yml` as a list per group.
- renderer#req-15: render a folder listed under `resources` as an index page of its files. Render an OpenAPI file in it as an API reference.

## Specifications
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


- renderer#req-16: render each requirement file with its `title` and `description`, the titles of the capabilities it realises, and whether it is in the core or in an extension, with the extension's title; then its requirements, then its body.
- renderer#req-17: render each requirement with its `id`, its `statement`, every classifier after defaults, its `interaction` and `contract`, its `documentation`, and links to its API operation and test. Give it the anchor `req-<id>`, unique across the book.
- renderer#req-18: render a deprecated requirement struck through, with its status shown.
- renderer#req-19: render a requirement index page: one row per numbered requirement, sorted by `id`, with its functionality, core or extension, the classifiers, `interaction`, and a link to the requirement.
- renderer#req-20: write `requirements.json` at the root of the version's book, with this shape, sorted by `id`:

  ```json
  {
    "urn": "govstack:spec:bb:wallet",
    "version": "1.2.0",
    "capabilities": [
      { "id": "custody", "title": "Credential Custody" }
    ],
    "extensions": [
      { "id": "issuance", "title": "Issuance", "requires": [] }
    ],
    "functionalities": [
      { "title": "Credential Intake", "realises": ["custody"], "conformance": "CORE" }
    ],
    "requirements": [
      { "id": "1", "status": "PUBLISHED", "level": "REQUIRED", "mutability": "IMMUTABLE",
        "interaction": "FILE", "functionality": "Credential Intake", "statement": "..." }
    ]
  }
  ```

- renderer#req-21: include in `requirements.json` every field passed by `requirements_parser#req-21`, so that the next version's checks can read them. The fields in the example above are the minimum.
- renderer#req-22: publish a JSON Schema for `requirements.json` in this repository, and validate the file against it before writing.
- renderer#req-23: include in `requirements.json` every requirement that has an `id`, whatever its status, including withdrawn ones, so that the next version's checks count their numbers. Leave out requirements without an `id`.
- renderer#req-24: render a requirement without an `id` (`DRAFT`, `CANDIDATE`, or `WITHDRAWN` before acceptance) marked as not normative, without a `req-` anchor, and leave it out of the requirement index. A `WITHDRAWN` requirement with an `id` is rendered struck through, as in req-18 (ARCH-ADR-5).
- renderer#req-25: render, on the requirement index page, the capabilities declared in `metadata.yml`, each with its description and the functionalities that realise it, marking a capability realised only by extensions; and the extensions, each with its description, role, the extensions it requires and its functionalities.

## Open

- [ ] The static site generator. It must render CommonMark with tables, Mermaid, and custom pages for requirements and terms. It must not need GitBook syntax.
- [ ] Where the book is deployed, by what, and with which credentials (see `index.md`, *Open*).
- [ ] Does the renderer also produce a PDF of the book?
