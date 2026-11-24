# TOC parser

Reads `toc.yml` and produces the ordered list of pages to render. The format is set in the GovStack Process, chapter *Compiling Publications*, section *toc.yml*. The `conformance-profiles` section of a specification is set in the Specification Framework, *Structure of Specifications* (ARCH-ADR-7, proposed).

- toc_parser#req-1: read `toc.yml` and fail if it has no `toc` key.
- toc_parser#req-2: accept the keys `introduction`, a body section, and `resources`, in that order. The body section is `chapters`, or for a specification `conformance-profiles`, or `requirements` in the legacy layout (`repository_layout#req-16`). Fail on any other key, or if more than one body section is present.
- toc_parser#req-3: resolve each page entry as a path relative to the folder named by its key, and fail if the file does not exist. The pages of a conformance profile are resolved by req-25.
- toc_parser#req-4: keep pages in the order they are listed.
- toc_parser#req-5: warn for every file in `introduction/`, `chapters/`, `conformance-profiles/`, `requirements/` or `resources/` that is not listed.

## Subsections

- toc_parser#req-6: under `chapters`, accept an entry that is a map with a single key. The key is the subsection title and its value is the list of the subsection's pages.

  ```yaml
  chapters:
    - Publication types:
        - first_type.md
    - closing_chapter.md
  ```

- toc_parser#req-7: fail on a subsection under `introduction`, `requirements` or `resources`.
- toc_parser#req-8: fail if a subsection entry contains another subsection. Subsections are one level deep.
- toc_parser#req-9: fail if a subsection has an empty title, has no pages, or has more than one key.
- toc_parser#req-10: fail if two subsections in `chapters` have the same title.
- toc_parser#req-11: resolve the pages of a subsection under `chapters` relative to `chapters/`, as for any other chapter. The subsection title does not name a folder.
- toc_parser#req-12: keep subsections and pages in the order they are listed, and pass each subsection title to the renderer as a heading in the table of contents.

## Conformance profiles

Under `conformance-profiles`, each entry is a conformance profile: a subsection whose key is the name of the profile's folder, and whose pages are its introduction and its functionality files (ARCH-ADR-7). req-8 and req-9 apply to these subsections.

```yaml
conformance-profiles:
  - credential-wallet:
      - introduction.md
      - functionalities/secure-storage.md
      - functionalities/credential-acquisition.md
  - verifiable-credentials:
      - introduction.md
      - functionalities/vc-acquisition.md
```

- toc_parser#req-23: fail on every entry under `conformance-profiles` that is not a subsection.
- toc_parser#req-24: fail if the key of a subsection under `conformance-profiles` is not the name of a folder in `conformance-profiles/`. The key is the id of the profile, not a title: the renderer titles the subsection with the `title` of the profile's introduction (`renderer#req-36`).
- toc_parser#req-25: resolve the pages of a profile relative to its folder, `conformance-profiles/<key>/`.
- toc_parser#req-26: fail if the first page of a profile is not `introduction.md`, or if `introduction.md` is listed in any other place.
- toc_parser#req-27: fail for every folder in `conformance-profiles/` that is not listed as a profile, and for every profile listed more than once.

## Entries that are not Markdown pages

`bb-template` lists folders under `resources` (`api/`, `test/`) and YAML files under `introduction` (`terminology.yml`, `references.yml`).

- toc_parser#req-13: accept an entry that ends with `/` under `resources` only. It names a folder relative to `resources/`. Fail if the folder does not exist.
- toc_parser#req-14: treat a listed folder as one page, and count every file under it as listed for toc_parser#req-5. How a folder is rendered is set in `renderer.md`.
- toc_parser#req-15: accept an entry that ends with `.yml` or `.yaml` only where `page_parser.md` knows how to render it: `terminology.yml` and `references.yml` under `introduction`. Fail on any other YAML entry.
- toc_parser#req-16: fail on an entry that ends with neither `.md`, `.yml`, `.yaml` nor `/`.

## Required sections

- toc_parser#req-17: fail if `introduction` is missing or empty.
- toc_parser#req-18: fail if the body section (`chapters`, `conformance-profiles` or `requirements`) is missing or empty.
- toc_parser#req-19: accept a missing or empty `resources`.
- toc_parser#req-20: fail if the same file is listed twice.
- toc_parser#req-21: fail if an entry is an absolute path or contains `..`.

## Output

- toc_parser#req-22: pass to the later stages an ordered tree: sections (`introduction`, the body section, `resources`), each a list of pages and subsections, each page with its path and kind (`markdown`, `terminology`, `references`, `folder`). A subsection of `conformance-profiles` carries its profile id instead of a title.

## Open

- [ ] *Compiling Publications* writes `terminology.yml` and `references.md`. The Specification Framework writes `terminology.md` and `references.md`. `bb-template` writes `terminology.yml` and `references.yml`. Which forms does the compiler accept?
- [ ] `bb-template/toc.yml` lists `purpose.md`, but *Structure of Specifications* does not have a `purpose.md` in `introduction/`.
- [ ] *Compiling Publications* describes `toc.yml` with `chapters` and `requirements`. It needs the `conformance-profiles` section and its subsections once ARCH-ADR-7 is taken.
