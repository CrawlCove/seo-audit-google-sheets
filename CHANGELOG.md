# Changelog

## 1.1.0, 2026-11-20

- Duplicate titles are only counted among pages that compete in search: a 200
  page that did not redirect, is not noindex and does not canonicalise to
  another URL. A `?sort=` variant whose canonical is the category, a noindex
  copy or `/blog` redirecting to `/blog/` no longer reads as a duplicate. Same
  rule as seo-mcp-server 1.1.0 and seo-crawl-export-js 1.1.0. To update an
  installed copy, paste the new `src/lib.js` over the old one in the Apps
  Script editor.

## 1.0.0 — 2026-09-29

Initial release.

- Crawl Cove menu with "Import export from Drive…" and "Paste export (JSON or CSV)…".
- Builds six sheets: Summary, Pages (29 export columns + issues on page,
  filter, status colouring), Issues, Issues by type, Pages by status, Title &
  meta flags (conditional formatting).
- Issue rules identical to crawlcove-mcp's get_issues.
- Reads the JSON export (crawlcove-export-spec 1.0.0) and the app's CSV export.
- `src/lib.js` is pure and tested under Node (9 tests); CI checks both files
  stay plain scripts for the Apps Script V8 runtime.
