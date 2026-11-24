# Tag parser

Reads the `{% … %}` tags in Markdown pages. A tag marks content that CommonMark cannot express: a callout, tabs, steps, a card that links to another page, a downloadable file, an API operation. The syntax follows GitBook, so that pages written in GitBook keep their meaning when compiled, but the set of tags is the compiler's own: it keeps the GitBook tags that a book needs, adds `raw` from Liquid, and refuses the rest.

The checker parses and checks tags, and writes them to the bundle in a canonical form (req-30). The renderer turns that form into HTML (`renderer#req-27` to `req-35`).

## Candidates

| Tag | Origin | Decision |
|---|---|---|
| `hint` | GitBook | Supported (req-12 to req-14) |
| `tabs`, `tab` | GitBook | Supported (req-15 to req-17) |
| `stepper`, `step` | GitBook | Supported (req-18, req-19) |
| `code` | GitBook | Supported (req-20, req-21) |
| `content-ref` | GitBook | Supported (req-22, req-23) |
| `file` | GitBook | Supported (req-24) |
| `embed` | GitBook | Supported as a link, never an iframe (req-25) |
| `openapi` | GitBook | Supported, against the OpenAPI files under `resources/` (req-26, req-27) |
| `include` | GitBook, Liquid | Supported, for files of the same repository (req-28, req-29) |
| `raw` | Liquid | Supported (req-11) |
| `openapi-operation` | GitBook | Not supported. Replaced by `openapi`, whose `src` defaults to `resources/api.yml` in a specification (req-9) |
| `swagger`, `swagger-description`, `swagger-parameter`, `swagger-response` | GitBook, deprecated by GitBook | Not supported. Replaced by `openapi` (req-9) |
| `columns`, `column` | GitBook | Not supported. Layout only (req-9) |
| `updates`, `update` | GitBook | Not supported. A changelog belongs in the release notes (req-9) |
| `abstract` | — | Out of scope of this file |

## Syntax

- tag_parser#req-1: recognise a tag as `{% <name> <attributes> %}`, where `<name>` is lowercase letters, digits and `-`, and `<attributes>` is zero or more `key="value"` pairs separated by spaces. A value is a double-quoted string in which `\"` and `\\` are the only escapes. `include` also accepts one double-quoted path in place of attributes (req-28).
- tag_parser#req-2: recognise a tag only when it is alone on its line, after at most the indentation of the block it is in. Do not recognise tags in fenced code blocks, indented code blocks, inline code or HTML blocks.
- tag_parser#req-3: close a tag that has a body with `{% end<name> %}`, alone on its line. Each tag in this file states whether its body is required, optional or not allowed. A tag with an optional body has one when its end tag follows in the same block before another tag of the same name opens.
- tag_parser#req-4: fail if a tag whose body is required is not closed, if an end tag has no matching open tag, or if tags overlap instead of nesting. Report the line of the open tag.
- tag_parser#req-5: parse the body of a tag as Markdown with the same rules as the page (`page_parser#req-5`), so that links, images and Mermaid blocks in a body are checked and rewritten like any other (`page_parser#req-7` to `req-18`).
- tag_parser#req-6: fail if a tag lacks an attribute this file marks as required.
- tag_parser#req-7: warn for every attribute this file does not list for that tag, and ignore it.
- tag_parser#req-8: warn for a tag whose name is not in the *Candidates* table, and pass it to the bundle as literal text, so that the reader sees what the author wrote.
- tag_parser#req-9: warn for a tag marked *Not supported* in the *Candidates* table, naming the replacement if there is one. Drop the tag lines and keep their bodies, so that no content is lost.
- tag_parser#req-10: warn for a line of prose that contains `{%` and is not a tag, and suggest `raw` (req-11) if the text is meant literally.
- tag_parser#req-11: `{% raw %}`, body required: pass the body to the bundle as literal text, without parsing tags in it. The body is still Markdown.

## Hint

A callout set apart from the text: a note, a tip, a caution.

- tag_parser#req-12: `{% hint style="…" %}`, body required. `style` is one of `info`, `success`, `warning`, `danger`, and defaults to `info`.
- tag_parser#req-13: warn if `style` has another value, and use `info`.
- tag_parser#req-14: accept a hint inside any tag except `code`, `raw` and another `hint`. Fail on a hint inside a hint.

## Tabs

Alternatives the reader picks between, such as the same example in several languages.

- tag_parser#req-15: `{% tabs %}`, body required, holding only `{% tab title="…" %}` tags, each with a required body and a required `title`. Fail on anything but blank lines between the tabs.
- tag_parser#req-16: fail on a `tab` outside `tabs`, and on a `tabs` with no `tab`.
- tag_parser#req-17: warn if two tabs of the same `tabs` have the same `title`.

## Stepper

A numbered procedure in which each step can hold several blocks.

- tag_parser#req-18: `{% stepper %}`, body required, holding only `{% step %}` tags, each with a required body. Fail on anything but blank lines between the steps.
- tag_parser#req-19: fail on a `step` outside `stepper`, and on a `stepper` with no `step`.

## Code

Options for one code block that a fenced block's info string cannot carry in CommonMark.

- tag_parser#req-20: `{% code title="…" lineNumbers="…" overflow="…" %}`, body required. Every attribute is optional. `lineNumbers` is `true` or `false` and defaults to `false`; `overflow` is `wrap` or `scroll` and defaults to `scroll`.
- tag_parser#req-21: fail if the body is not exactly one fenced code block. Warn if `lineNumbers` or `overflow` has another value, and use the default.

## Content reference

A card that links to another page of the publication or to another publication.

- tag_parser#req-22: `{% content-ref url="…" %}`, body optional. `url` is required and is a link: req-5 applies to it as to a link in the body, including `govstack:` links (`page_parser#req-12`).
- tag_parser#req-23: take the card's title from the target page's title (`page_parser#req-3`) when `url` is a page of the publication, and from the text of the first link in the body otherwise. Warn if there is neither. GitBook writes the body as a link to the same target; ignore the rest of the body.

## File

A file the reader downloads.

- tag_parser#req-24: `{% file src="…" %}`, body optional. `src` is required and is checked as an image path is (`page_parser#req-7`, `req-9`, `req-11`, `req-16` to `req-18`). The body is the caption.

## Embed

External content. The book is an archive of one version, and content served from elsewhere changes or disappears, so the book never embeds it.

- tag_parser#req-25: `{% embed url="…" %}`, body optional. `url` is required and must be an absolute `https` URL. Fail otherwise. The body is the caption. Pass the embed to the bundle as a link to `url` with its caption, not as embedded content.

## OpenAPI operation

One operation of the publication's API, rendered from the OpenAPI file in place.

- tag_parser#req-26: `{% openapi src="…" operation="…" %}`, body optional and ignored. `src` names an OpenAPI file under `resources/`. It is required, except in the spec profile, where it defaults to `/resources/api.yml` (Specification Framework, *Requirements*, *Interaction types*). `src` follows the path rules of `page_parser#req-16` to `req-18`. Name the operation either by `operation`, its `operationId`, or by `path` and `method`, as GitBook does. Fail if neither or both are given.
- tag_parser#req-27: fail if `src` is not a valid OpenAPI 3 file, or if it has no operation with that `operationId`, or with that `path` and `method`. Pass the operation's `operationId` to the bundle in every case, so that the renderer links it to the requirements whose `api_operation_id` names it (`requirements_parser#req-13`).

## Include

Text that several pages share, kept once.

- tag_parser#req-28: `{% include "…" %}` or `{% include src="…" %}`, no body. The path names a Markdown file of the same repository, with the path rules of `page_parser#req-7`, `req-11` and `req-16`. Replace the tag with the file's body, without its front matter, before any other check of the page, and report diagnostics in the included text at the line of the included file.
- tag_parser#req-29: fail on an include cycle. Warn if an included file is also listed in `toc.yml`, because its text then appears twice. Do not warn under `toc_parser#req-5` for a Markdown file that a listed page includes.

## Bundle

- tag_parser#req-30: write tags to the bundle pages in canonical form: includes expanded, defaults filled in, unknown attributes removed, every attribute written as `key="value"` in the order of this file, and links in attributes rewritten like links in the body (`page_parser#req-10`). The renderer reads this form only, and the bundle schema states its grammar.

## Open

- [ ] The tags are proposed in the GovStack Process, chapter *Writing Pages*, with PROC-ADR-5. Cite it once the record is accepted.
- [ ] Where included files live (req-28). Proposed in *Writing Pages*: next to the pages that include them, unlisted in `toc.yml` (req-29), so the layout does not change.
- [ ] `{% abstract %}`: decided separately. Until then it falls under req-8.
- [ ] Should the compiler offer a fix that rewrites GitBook's `<figure><img src=".gitbook/assets/…">` into an image under `assets/`? See `page_parser#req-20`.
