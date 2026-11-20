<a href="https://crawlcove.com/download?utm_source=github&utm_medium=readme&utm_campaign=seo-audit-google-sheets"><img src="https://raw.githubusercontent.com/CrawlCove/.github/main/banners/seo-audit-google-sheets.svg" width="1200" alt="seo-audit-google-sheets: An Apps Script add-on that turns a site crawl export into an audit workbook: pages, issues by type, pages by status and title and meta flags." /></a>

# seo-audit-google-sheets

An **SEO audit in Google Sheets**: an Apps Script add-on that imports a [Crawl Cove](https://crawlcove.com/?utm_source=github&utm_medium=seo-audit-google-sheets) crawl export into a standard SEO audit workbook: a **Summary**, every **Page** with its 29 export columns, an **Issues** list, **Issues by type**, **Pages by status**, and **Title & meta flags** with conditional formatting. One menu item, no copy-pasting formulas, and the issue rules are the same ones [seo-mcp-server](https://github.com/CrawlCove/seo-mcp-server) uses, so the sheet and an AI assistant agree on the numbers.

Works with both export formats the desktop app writes from Reports → Export: JSON ([crawlcove-export-spec](https://github.com/CrawlCove/seo-crawl-export-spec)) and CSV. A Screaming Frog export works too once converted with [screaming-frog-export-converter](https://github.com/CrawlCove/screaming-frog-export-converter).

## Install (two minutes, no add-on store)

1. Open a Google Sheet (a new one is fine). **Extensions → Apps Script.**
2. Delete the default `Code.gs` contents. Create two files: `lib.gs` with the contents of [`src/lib.js`](src/lib.js) and `Code.gs` with the contents of [`src/Code.js`](src/Code.js). (Apps Script calls them `.gs`; the code is plain JavaScript.)
3. Save, then reload the spreadsheet. A **Crawl Cove** menu appears.
4. **Crawl Cove → Import export from Drive…** (type the file name or ID of a Crawl Cove export you've uploaded to Drive) or **Paste export (JSON or CSV)…**.

The first run asks for permission to edit this spreadsheet, show dialogs, and read your Drive (read-only, only to fetch the file you name). The script never sends data anywhere: it reads the file you point it at and writes sheets in the current workbook.

With [clasp](https://github.com/google/clasp): copy `.clasp.json.example` to `.clasp.json`, set your script ID, `npm run push`.

## What you get

| Sheet | Contents |
|---|---|
| Summary | Pages crawled, indexable pages, pages with issues, issue count, export time, run id, whether the crawl hit its page cap. |
| Pages | One row per crawled URL with all 29 export columns (status, indexability, title/meta and their lengths, canonical, robots, H1 count, word count, links, images, response time, size, redirect hops, fetch error, schema blocks, hreflang count, fingerprint, findings) plus "Issues on page". Filter and frozen header. 3xx rows amber, 4xx/5xx red. |
| Issues | One row per issue: type, URL, detail. |
| Issues by type | Count per type with a one-line meaning, in a fixed order so you can chart it. |
| Pages by status | Count per HTTP status (plus "No response" / "Fetch error"). |
| Title & meta flags | Every 200 page with title and meta lengths flagged `missing` / `too long` / `short` / `ok`, colour-coded. |

Issue rules (identical to seo-mcp-server's `get_issues`): broken page (4xx/5xx or fetch error), missing / long (>60) / duplicate title, missing / long (>160) meta description, missing / multiple H1, noindex, redirect chain (2+ hops), missing canonical on an indexable page. Title, meta and H1 rules only apply to 200 pages, and a duplicate title only counts between pages that did not redirect, are not noindex and do not canonicalise elsewhere.

Re-importing overwrites the six sheets and leaves any other sheets in the workbook alone, so you can keep your own analysis tabs next to the data.

## Template

A ready-made template workbook with the script installed and charts on the Summary tab is coming; until then the two-minute install above is the way in.

## Development

`src/lib.js` is the pure part (parsing, issue rules, sheet rows) and runs unchanged in Node, so it has a test suite: `npm test`. `src/Code.js` is the Apps Script glue (menu, dialogs, `SpreadsheetApp` writes). Both must stay plain scripts (no `import`/`export`; CI checks) because Apps Script's V8 runtime has no module system.

## Works with CrawlCove

The export comes from [Crawl Cove](https://crawlcove.com/?utm_source=github&utm_medium=seo-audit-google-sheets), the desktop SEO crawler: crawl a site, open Reports, Export JSON or CSV, import here. The app's own report ranks fixes by impact and keeps history over time; the sheet is for sharing with a client or building your own pivots.

This repo has its own page on crawlcove.com: [Crawl Cove Google Sheets add-on](https://crawlcove.com/open-source/crawlcove-sheets?utm_source=github&utm_medium=seo-audit-google-sheets).

## Related tools

<!-- related:start, kept in step by CrawlCove/.github scripts/related.mjs -->
| Repo | What it does |
|---|---|
| [seo-crawler-cli](https://github.com/CrawlCove/seo-crawler-cli) | Crawl a site from a script or CI. Checks status codes, redirect chains, titles, meta descriptions, H1s, canonicals, noindex and broken links, and fails the build on regressions. |
| [seo-audit-action](https://github.com/CrawlCove/seo-audit-action) | Crawl a staging or preview URL on every pull request. Fails the check on broken links, missing titles, noindex and redirect chains, with the details as a PR comment. |
| [seo-mcp-server](https://github.com/CrawlCove/seo-mcp-server) | Crawl a site, list SEO issues, inspect pages and find broken links from inside Claude, Cursor and other AI assistants. |
| [claude-seo-plugin](https://github.com/CrawlCove/claude-seo-plugin) | Seven SEO skills for Claude Code backed by a real site crawl: audits, broken links, redirects, titles and fixes, from a chat. |
| [seo-chrome-extension](https://github.com/CrawlCove/seo-chrome-extension) | On-page SEO checker with an AI visibility report: which AI crawlers can read the page, snippet blocks, llms.txt and extractability. Nothing leaves the browser. |
| [xml-sitemap-validator](https://github.com/CrawlCove/xml-sitemap-validator) | Check a sitemap or sitemap index against the sitemaps.org protocol and search engine limits. Flags bad URLs, lastmod dates and cross-host entries. |
| [robots-txt-tester](https://github.com/CrawlCove/robots-txt-tester) | Fetch and lint a robots.txt, test which URLs each crawler may fetch and see the deciding line. Fails deploys that block the site. |
| [redirect-chain-checker](https://github.com/CrawlCove/redirect-chain-checker) | Follow every hop of a URL's redirects and flag chains, loops, HTTPS downgrades, meta refreshes and redirects to error pages. |
| [hreflang-checker](https://github.com/CrawlCove/hreflang-checker) | Validate the hreflang tags on a page or across a whole XML sitemap, including self-reference, x-default and return tags. |
| [schema-markup-validator](https://github.com/CrawlCove/schema-markup-validator) | Check every schema.org node on a page against the required and recommended properties for rich results. |
| [seo-test-site](https://github.com/CrawlCove/seo-test-site) | A deliberately broken SEO test site with 54 planted problems and the expected findings, for testing any SEO crawler. |
| [seo-crawl-export-spec](https://github.com/CrawlCove/seo-crawl-export-spec) | An open format for site crawl data: JSON Schema and CSV column reference, with a real sample export and a validator script. |
| [seo-crawl-export-js](https://github.com/CrawlCove/seo-crawl-export-js) | Load, query and convert site crawl data in JavaScript and TypeScript, with typed pages, issues by type and status filters. |
| [screaming-frog-export-converter](https://github.com/CrawlCove/screaming-frog-export-converter) | Turn an SEO Spider internal_all.csv into the open JSON crawl export format, with a report of which columns carried over. |
| [wordpress-seo-connector](https://github.com/CrawlCove/wordpress-seo-connector) | Bulk-apply fixes from the Crawl Cove desktop crawler to Yoast SEO, Rank Math, SEOPress or AIOSEO, with a change log and one-click revert. |
<!-- related:end -->

## License

MIT, see [LICENSE](LICENSE).
