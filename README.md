The `docs/` folder has the [MarkDown](https://guides.github.com/features/mastering-markdown) content that is rendered to <https://biocompute-objects.github.io/bco-ro/> by [GitHub pages](https://pages.github.com/).

A file like `docs/intro.md` appears rendered as <https://biocompute-objects.github.io/bco-ro/intro.html> You may crosslink pages using relative links like `[background](background.md)` - the extension `.md` is replaced with `.html` in rendering.

Note that files outside `docs` are not accessible within `github.io` and must be linked to by absolute URLs, e.g. <https://github.com/biocompute-objects/bco-ro/README.md> (this file)

It is more important that pages render well at <https://biocompute-objects.github.io/bco-ro/> than in this  preview within the GitHub repository, as some MarkDown features only apply to the GitHub Pages (e.g. the `{:toc}` macro).

### Using the jekyll-rtd-theme

Note that the specification `1.1` onwards uses split files for different sections, these are indexed by the [RunDocs theme](https://rundocs.io/) aka [rundocs/jekyll-rtd-theme](https://github.com/rundocs/jekyll-rtd-theme). The `---` preamble is required on all `*.md` files to help the theme, e.g.:

```
---
layout: default
title: Introduction
excerpt: |
  This is the introduction.
sort: 5
---
```

Tip: Only add `sort` for numbered sections. Unfortunately the number is shown as-is (no `GOTO 20` tricks), leading to frequent renumbering.

#### Hiding a section

To hide a section (`README.md`) or other file from the main table of content, e.g. a draft, add `exclude: true` to its preamble:

```
---
layout: default
exclude: true
title: Draft section
---
```

This is checked by a crude filter in the overriding [_includes/reset/site_pages.liquid](_includes/reset/site_pages.liquid). This file might need to be moved/updated when upgrading the theme. 


#### Sections and child pages

Note that although section folders have a `README.md` (alternatively `index.md`) - it is not generally shown in the left-hand menu, only in the breadcrumb. It is therefore recommended to make these index page minimal and only include a list of subpages of that section, using this Liquid include:

```
{% include list.liquid all=true %}
```

This is also helpful for navigating drafts as `exclude: true` does not show their sections in the left-hand menu.

#### Theme config

The theme is locked to a fixed version in [_config.yml](_config.yml) to avoid unexpected upgrade surprises. This file also specifies some site-wide properties like copyright.

### References

The [_includes/references.liquid](_includes/references.liquid) file includes Markdown [hyperlink references](https://kramdown.gettalong.org/syntax.html#reference-links) that can be used on shortform, e.g. `[CreativeWork]` gets expanded to `[CreativeWork](http://schema.org/CreativeWork)`  or `[creative work][CreativeWork]` becomes  `[creative work](http://schema.org/CreativeWork)`. This only works on `*.md` pages that have the `{% include references.liquid %}` footer.


{% include list.liquid all=true %}
