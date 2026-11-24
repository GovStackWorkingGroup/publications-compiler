# Architecture

## Projects

```text
.
├── checker/          Python package: reads a publication, runs every check, writes diagnostics and the bundle
│   ├── pyproject.toml
│   ├── src/govstack_compiler/
│   └── tests/
├── renderer/         Astro Starlight project: reads a bundle, writes the book
│   ├── package.json
│   └── src/
├── schemas/          the contract between the two
│   ├── bundle.schema.json
│   └── requirements.schema.json
├── .github/workflows/
│   ├── ci.yml        CI of this repository
│   └── compile.yml   the reusable workflow publications call (workflow#req-1)
├── requirements/     what the compiler does
└── plan/             how we build it
```

Both projects live in one repository, so that one tag pins the workflow, the checker and the renderer together (`workflow#req-4`).

## Flow

```text
publication repo ─► checker ─► diagnostics (text, JSON, annotations)
                       │
                       └─► bundle/ ─► renderer ─► book/ ─► GitHub Pages
                           (on release, or with --render)
```

The checker runs every check first. It writes the bundle only when the book is to be rendered (`index#req-7`, `index#req-8`, `renderer#req-1`, `req-2`). The renderer is started by the workflow, or by the checker's `--render` for a local preview.

## The bundle

A folder, validated against `schemas/bundle.schema.json` before it is written.

```text
bundle/
├── bundle.json         the publication, the version, the navigation, and the index of pages
├── pages/              one file per page, Markdown with links already rewritten
├── assets/             every file a page uses
└── requirements.json   spec profile only; the file published next to the book
```

`bundle.json` holds:

- The publication: `name`, the publication type, `abstract`, `urn`, `owner_group`, `repository`, and the version being compiled with its version URL, `release_date`, `short_description`, whether it is a pre-release, and the list of every version.
- The authors with their roles.
- The navigation: sections, subsections and pages in `toc.yml` order.
- Each page with its bundle path, kind (`markdown`, `terminology`, `references`, `folder`, `conformance-profile`, `requirements`), title, source path and source URL at the tag; and the generated pages, the requirement index and one view per option key (`renderer#req-41`, `req-43`).
- For structured pages, the parsed data: term entries, reference groups, the files of a folder, for the introduction of a conformance profile its declaration, and for a functionality file its functionality fields, its conformance profile, and its requirements with classifiers after defaults, verification method, options and conditions.

Markdown bodies stay Markdown. Any renderer can turn CommonMark into HTML, so the bundle does not tie the book to one engine. Mermaid blocks stay fenced `mermaid` blocks.

The schema is versioned. A change that a renderer cannot read without changes is a new major version of the schema, and a new major version of the compiler (`workflow#req-9`).
