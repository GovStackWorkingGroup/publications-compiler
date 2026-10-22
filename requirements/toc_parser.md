# TOC parser

Reads `toc.yml` and produces the ordered list of pages to render. The format is set in the GovStack
Process, chapter *Compiling Publications*, section *toc.yml*.

- toc_parser#req-1: read `toc.yml` and fail if it has no `toc` key.
- toc_parser#req-2: accept the keys `introduction`, `chapters` (or `requirements` for a
  specification) and `resources`, in that order. Fail on any other key, or if both `chapters` and
  `requirements` are present.
- toc_parser#req-3: resolve each page entry as a path relative to the folder named by its key, and
  fail if the file does not exist.
- toc_parser#req-4: keep pages in the order they are listed.
- toc_parser#req-5: warn for every file in `introduction/`, `chapters/`, `requirements/` or
  `resources/` that is not listed.

## Subsections

- toc_parser#req-6: under `chapters`, accept an entry that is a map with a single key. The key is
  the subsection title and its value is the list of the subsection's pages.
  ```yaml
  chapters:
    - Publication types:
        - first_type.md
    - closing_chapter.md
  ```
- toc_parser#req-7: fail on a subsection under `introduction`, `requirements` or `resources`.
- toc_parser#req-8: fail if a subsection entry contains another subsection. Subsections are one level
  deep.
- toc_parser#req-9: fail if a subsection has an empty title, has no pages, or has more than one key.
- toc_parser#req-10: fail if two subsections in `chapters` have the same title.
- toc_parser#req-11: resolve the pages of a subsection relative to `chapters/`, as for any other
  chapter. The subsection title does not name a folder.
- toc_parser#req-12: keep subsections and pages in the order they are listed, and pass each
  subsection title to the renderer as a heading in the table of contents.

## Entries that are not Markdown pages

`bb-template` lists folders under `resources` (`api/`, `test/`) and YAML files under
`introduction` (`terminology.yml`, `references.yml`).

- toc_parser#req-13: accept an entry that ends with `/` under `resources` only. It names a folder
  relative to `resources/`. Fail if the folder does not exist.
- toc_parser#req-14: treat a listed folder as one page, and count every file under it as listed for
  toc_parser#req-5. How a folder is rendered is set in `renderer.md`.
- toc_parser#req-15: accept an entry that ends with `.yml` or `.yaml` only where `page_parser.md`
  knows how to render it: `terminology.yml` and `references.yml` under `introduction`. Fail on any
  other YAML entry.
- toc_parser#req-16: fail on an entry that ends with neither `.md`, `.yml`, `.yaml` nor `/`.

## Required sections

- toc_parser#req-17: fail if `introduction` is missing or empty.
- toc_parser#req-18: fail if `chapters` (or `requirements` for a specification) is missing or
  empty.
- toc_parser#req-19: accept a missing or empty `resources`.
- toc_parser#req-20: fail if the same file is listed twice.
- toc_parser#req-21: fail if an entry is an absolute path or contains `..`.

## Output

- toc_parser#req-22: pass to the later stages an ordered tree: sections (`introduction`, `chapters`
  or `requirements`, `resources`), each a list of pages and subsections, each page with its path
  and kind (`markdown`, `terminology`, `references`, `folder`).

## Open

- [ ] *Compiling Publications* writes `terminology.yml` and `references.md`. The Specification
  Framework writes `terminology.md` and `references.md`. `bb-template` writes `terminology.yml`
  and `references.yml`. Which forms does the compiler accept?
- [ ] `bb-template/toc.yml` lists `purpose.md`, but *Structure of Specifications* does not have a
  `purpose.md` in `introduction/`.
