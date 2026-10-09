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
