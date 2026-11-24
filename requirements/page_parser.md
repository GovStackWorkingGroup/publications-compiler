# Page parser

Reads every page listed in `toc.yml` and prepares it for the renderer. A page is a Markdown file
with optional YAML front matter, or one of the YAML pages listed in `toc_parser#req-15`.

## Markdown pages

- page_parser#req-1: read each Markdown page as UTF-8. Fail if it is not valid UTF-8.
- page_parser#req-2: read the YAML front matter between a first line `---` and the next line
  `---`. Fail if the front matter does not parse or is not a map.
- page_parser#req-3: take the page title from the front matter `title`. If there is none, take the
  first level-1 heading. If there is neither, warn and use the file name.
- page_parser#req-4: accept the front matter keys `title`, `status`, `authors` and `references` on any page. In the spec profile, also accept the keys of a functionality file (`requirements_parser.md`) on a functionality file, and the keys of a profile declaration (`conformance_profile_parser#req-6`) on the `introduction.md` of a conformance profile. Warn for every other key.
- page_parser#req-5: parse the body as CommonMark with GitHub tables, task lists and fenced code
  blocks.
- ~~page_parser#req-6~~: `DEPRECATED`. Tags are parsed by `tag_parser.md`; paths under `.gitbook/` are req-20.
- page_parser#req-19: parse `{% … %}` tags with `tag_parser.md` before the checks of links and assets, so that those checks see included text and the links in tag attributes.
- page_parser#req-20: warn on a link, image or `<figure>` whose path is under `.gitbook/`, and say that the file belongs under `assets/`.

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
- page_parser#req-16: resolve a link or image path that starts with `/` from the root of the publication repository, not from the folder of the page. A page anywhere in the repository references an asset as `/assets/<path>`, for example `![Flow](/assets/diagrams/flow.png)` in `requirements/intake.md` and in `introduction/overview.md` alike. GitHub resolves the same path from the repository root when browsing the source, so the page shows its images in both places.
- page_parser#req-17: warn if a page references a file under `assets/` with a path relative to the page, such as `../assets/flow.png`, and give the `/assets/…` form in the message. Resolve the relative path anyway, so the page still renders.
- page_parser#req-18: each publication keeps its own `assets/`. A path that starts with `/` names a file of this repository only, so req-7 and req-11 apply to it. A page does not reference the assets of another publication; it links to that publication with req-12.

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
