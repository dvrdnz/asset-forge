# docs-site

A zero-build GitHub Pages template for browsing Markdown files with a sidebar, dark/light mode, and multi-file navigation.

## What this is

`templates/docs-site/index.html` is the source template for the docs site used by the repository root `index.html`. It is meant to be copied into another repo and adapted there.

The page:

* renders Markdown files with `marked.js`
* builds a sidebar from a file list in JavaScript
* supports light and dark mode
* works without a build step or package manager

## How to use it

1. Copy `templates/docs-site/index.html` into the root of your target repository.
2. Edit the `FILES` array in the script section to point at your Markdown files.
3. Add the Markdown files you want to expose in the nav.
4. Enable GitHub Pages or serve the file over HTTP locally.
5. Open the page and the sidebar will populate from the files that actually exist.

## File list

The `FILES` array controls the navigation.

Each entry contains:

* a label for the sidebar
* a list of filename candidates in priority order
* an icon name

At startup, the template probes each candidate with `HEAD` and uses the first one that resolves. Entries with no match are skipped instead of showing dead links.

## Requirements

* Files must be served over HTTP. `fetch()` does not work reliably on `file://`.
* The page loads `marked.js` from a CDN.
* Google Fonts are loaded from `fonts.googleapis.com`.

## Security note

The Markdown renderer uses `marked.parse(md)` and writes the result directly into `innerHTML`.

That is acceptable for trusted repository content. It is **not** safe for untrusted Markdown unless you sanitize the rendered HTML first.

