# Page parser

Reads every page listed in `toc.yml` and prepares it for the renderer. A page is a Markdown file
with optional YAML front matter, or one of the YAML pages listed in `toc_parser#req-15`.

## Markdown pages

- page_parser#req-1: read each Markdown page as UTF-8. Fail if it is not valid UTF-8.
- page_parser#req-2: read the YAML front matter between a first line `---` and the next line
  `---`. Fail if the front matter does not parse or is not a map.
- page_parser#req-3: take the page title from the front matter `title`. If there is none, take the
  first level-1 heading. If there is neither, warn and use the file name.
- page_parser#req-4: accept the front matter keys `title`, `status`, `authors` and `references` on
  any page. In the spec profile, also accept the keys of a requirement file (see
  `requirements_parser.md`). Warn for every other key.
- page_parser#req-5: parse the body as CommonMark with GitHub tables, task lists and fenced code
  blocks.
- page_parser#req-6: warn on GitBook syntax that will not render, such as `{% hint %}`,
  `{% embed %}`, `{% tabs %}` and `<figure>` with paths under `.gitbook/`.

## Links and assets

- page_parser#req-7: fail if a relative link or image points to a file that does not exist in the
  repository.
- page_parser#req-8: warn if a relative link points to a Markdown page that is not listed in
  `toc.yml`. That page is not rendered, so the link will break.
- page_parser#req-9: warn if an image is not under `assets/`.
- page_parser#req-10: rewrite links between pages to the paths of the rendered pages.
- page_parser#req-11: fail if a link points outside the repository.
- page_parser#req-12: accept a link to another GovStack publication written as its `urn` with an
  optional fragment, for example `govstack:spec:bb:wallet#req-4`. Resolve it with
  `records_client.md` to the URL of the latest version. Warn if it does not resolve. Skip with
  `--offline`.

## Diagrams

- page_parser#req-13: pass fenced code blocks with the language `mermaid` to the renderer as
  diagrams.

## YAML pages

- page_parser#req-14: parse `terminology.yml` with `terminology_parser.md`.
- page_parser#req-15: parse `references.yml` as a map from group name to a list of references,
  where each reference is a string. Warn on an empty group. An empty file is a page with no
  references.

## Open

- [ ] Cross-publication links (req-12): is the `urn#fragment` form agreed? *GovStack Namespaces*
  addresses chapters by fragment, and the Requirements Model cites requirements as
  `govstack-bb-wallet#req-1`, which is a different form.
- [ ] Should the compiler check external `https` links? It makes runs slow and flaky. A weekly job
  could do it instead.
