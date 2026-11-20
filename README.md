# Localnet views

The view source that Localnet, a browser extension that is a personal search engine, starts with. `source.json` lists
the repo of every view; Localnet reads each one's `source.json` and the files it names, and offers what they give:

- a **listing** view, how a list of results looks, in Show as (beside Localnet's own List and Table);
- a **content** view, how a page looks in Read, as Read's Look (beside Localnet's own, Clean).

To add a view, give it a repo of its own (see any listed here) and add the repo's address to `sources`, or add the repo
itself as a view source in Localnet (Library → View sources → New view source…).

| Views | Repo |
| --- | --- |
| Hacker News (listing, content) | [localnet-view-hackernews](https://github.com/opowell/localnet-view-hackernews) |
| Reddit and Reddit compact (listing), Reddit (content) | [localnet-view-reddit](https://github.com/opowell/localnet-view-reddit) |
| The Guardian (listing, content) | [localnet-view-guardian](https://github.com/opowell/localnet-view-guardian) |

## Writing a view

A view is markup and a stylesheet, never code: an extension page runs no script it did not ship with. Its repo's
`source.json` names its files:

```json
{
  "name": "Hacker News",
  "views": [
    { "name": "Hacker News", "kind": "listing", "hint": "Numbered rows, points and comments", "template": "listing.html", "style": "listing.css" },
    { "name": "Hacker News", "kind": "content", "hint": "Grey on beige, comments indented", "template": "content.html", "style": "content.css" }
  ]
}
```

A template is HTML with a few bindings:

| Written | Means |
| --- | --- |
| `{{ row.title }}` | a value as text, in text or in an attribute; `{{ row.points \|\| '–' }}` the first that is set; `!a` whether a is not |
| `data-for="row in rows"` | the element once per entry, with `$index`, `$number`, `$first` and `$last` |
| `data-if="row.picture"`, `data-unless="…"` | the element only where the value is set (or is not) |
| `data-class-read="row.read"` | the class `read` where the value is set |
| `data-open="row"` | a link opening the row in Localnet (Read, for a result); ⌘-click opens it in a new tab |

A link to a web page opens in a new tab. Scripts, frames, forms, styles and event attributes are left out.

A **listing** template is given `rows` (the page of rows shown) and `count` (how many match in all). Each row has
`rank`, `title`, `url` (the page, where it is one), `site` (its host), `source` (where on its site it comes from:
`reddit.com/r/gamedev`), `place` (the same without its site: `r/gamedev`), `picture`, `points`, `comments`, `author`, `ago` ("3 hr. ago"), `unread`, `read`, `saved`,
`type` ("Results"), `metrics` and `details` (each `[{ label, text }]`), and `fields`, every field as Localnet has it.

A **content** template is the head drawn over Read's page in place of its own, given `item`: `title`, `url` (what the
title links to: a discussion's article), `page` (the page read), `site`, `author`, `authors` (`[{ name, url }]`), `ago`,
`time`, `points`, `comments`, `length` ("1,234 words · 6 min") and `details`. A content view may give a stylesheet
alone, restyling Read as it is.

A stylesheet is kept to the view's own box: every rule is nested under it, so `.title { … }` styles the view's titles
and nothing else, and `& { … }` the box itself. A content view's rules reach Read's page by its classes
(`.reader-viewport`, `.reader-flow` and its `p`, `h2`, `h3`, `.reader-quote`, `.reader-item`, `.reader-code`,
`.reader-table`, `.reader-figure`; `.reader-comments`, `.reader-comment`, `.reader-comment-head`,
`.reader-comment-author`, `.reader-comment-meta`, `.reader-comment-replies`; the head's own `.lnv-head`) and outweigh
Read's own rules. Localnet is dark unless the light theme is chosen, or the system's light one is, as
`:root[data-theme='light'] &` and `@media (prefers-color-scheme: light) { :root:not([data-theme='dark']) & { … } }` say.
An `@import` is left out.
