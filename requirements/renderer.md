# Renderer

Renders the book: a static website of one version of a publication. The outputs are set in the
GovStack Process, chapter *Compiling Publications*, section *Outputs*.

## When it runs

- renderer#req-1: render only on release, only for a version without a pre-release suffix, and
  only if no check reported an error (`index#req-8`).
- renderer#req-2: render on any run when given `--render <dir>`, so that a Working Group can
  preview the book locally. A preview is not deployed.

## The book

- renderer#req-3: write the book of the version to `<output>/<url_suffix>/`.
- renderer#req-4: if the version is marked `latest`, also write it to the path of `url`.
- renderer#req-5: title the book with `name` followed by the name of the publication type, read
  from the profile and the `urn`. For example, `name: Wallet` and `urn: govstack:spec:bb:wallet`
  give *Wallet Building Block Specification*.
- renderer#req-6: render a front page with the title, `abstract`, the version, its `release_date`
  and `short_description`, the `urn`, the owner group and the authors with their roles.
- renderer#req-7: render a navigation that follows `toc.yml`: sections, then subsections as
  headings, then pages, in the order listed.
- renderer#req-8: render a version list from `versions` in `metadata.yml`, with a link to each
  released version at its `url_suffix`.
- renderer#req-9: render every page listed in `toc.yml` and no other page.
- renderer#req-10: copy `assets/` and every file a page links to into the book.
- renderer#req-11: render `mermaid` blocks as diagrams.
- renderer#req-12: render a link to the source of each page in `repository`, at the release tag.

## Special pages

- renderer#req-13: render `terminology.yml` with the layout of *Structure of a Term entry*, *The
  template*: entry number, preferred term in bold, abbreviation, definition, notes as "Note *n* to
  entry", and the source in square brackets. Give each entry an anchor from its term.
- renderer#req-14: render `references.yml` as a list per group.
- renderer#req-15: render a folder listed under `resources` as an index page of its files. Render
  an OpenAPI file in it as an API reference.

## Specifications

- renderer#req-16: render each requirement file with its `title` and `capability`, then its
  requirements, then its body.
- renderer#req-17: render each requirement with its `id`, its `statement`, every classifier after
  defaults, its `documentation`, and links to its API operation and test. Give it the anchor
  `req-<id>`, unique across the book.
- renderer#req-18: render a deprecated requirement struck through, with its status shown.
- renderer#req-19: render a requirement index page: one row per requirement, sorted by `id`, with
  the classifiers and a link to the requirement.
- renderer#req-20: write `requirements.json` at the root of the version's book, with this shape,
  sorted by `id`:

  ```json
  {
    "urn": "govstack:spec:bb:wallet",
    "version": "1.2.0",
    "requirements": [
      { "id": "1", "status": "PUBLISHED", "level": "REQUIRED", "mutability": "IMMUTABLE",
        "statement": "..." }
    ]
  }
  ```

- renderer#req-21: include in `requirements.json` every field passed by
  `requirements_parser#req-21`, so that the next version's checks can read them. The fields in the
  example above are the minimum.
- renderer#req-22: publish a JSON Schema for `requirements.json` in this repository, and validate
  the file against it before writing.

## Open

- [ ] The static site generator. It must render CommonMark with tables, Mermaid, and custom pages
  for requirements and terms. It must not need GitBook syntax.
- [ ] Where the book is deployed, by what, and with which credentials (see `index.md`, *Open*).
- [ ] Does the renderer also produce a PDF of the book?
