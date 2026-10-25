# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and this project adheres to [Semantic Versioning](https://semver.org/).

## [1.1.1] - 2026-10-25

### Added
- **Homebrew installation** — `brew install fullstackspectrum/vulnly/vulnly`, on macOS and Linux. The formula lives in the [fullstackspectrum/homebrew-vulnly](https://github.com/fullstackspectrum/homebrew-vulnly) tap and is updated with each stable release; betas remain on PyPI only.

### Changed
- GitHub links now point at the `fullstackspectrum` account, which was renamed from `colinmoynes`. This covers the link in every report's footer, the project links on PyPI, the security policy and the README. GitHub redirects the old URLs, but only until someone else registers the name.

## [1.1.0] - 2026-09-08

### Added
- **Cloudsmith v2 vulnerabilities API support** (`--source cloudsmith-osv`), for the OSV advisory records served by `GET /v2/packages/{package_slug_perm}/vulnerabilities/`. The v1 CLI format the `cloudsmith vulnerabilities` command emits is unchanged and still auto-detected; the v2 format is auto-detected too. Cloudsmith's precomputed `best_severity` supplies the severity and CVSS score, falling back to the raw OSV `severity` arrays. Malware records are reported as Critical, since they carry no CVSS vector and would otherwise sort below lesser findings, and vendor threat levels outside the five report severities are mapped onto them. Duplicate records — returned when the endpoint is queried with `primary_records=false` — are collapsed.
- `--package`, `--package-version` and `--repository`, which set the package identity shown in the report. The v2 OSV payload is advisory data and names no package, so there is nothing to derive it from; the flags apply to any source.

## [1.0.0] - 2026-08-13

First stable release.

### Added
- Sortable table columns, with keyboard support. Sorting applies to the whole result set rather than the page currently loaded, so sorting by CVSS with 50 of 500 rows shown surfaces the true highest scores. Reports open worst-first, and rows equal on the sorted column fall back to worst-first so sorting by package still lists the most urgent finding in each group.
- A **Fixable** count of findings that have a fix available, and a **Fixable only** filter that combines with the severity filters and search.
- A **Top Affected Packages** breakdown showing where findings are concentrated.
- **CSV export** of the current filtered and sorted view, covering every matching row rather than only those on screen. Fields that a spreadsheet would otherwise execute as a formula are escaped so they are read as text.
- Reports now carry a browser tab icon.
- A published [security policy](SECURITY.md) for reporting vulnerabilities in Vulnly itself.
- A `py.typed` marker, so type checkers see the package's annotations. The `Typing :: Typed` classifier was previously declared without one.

### Changed
- **Reports are fully offline.** The charting library and font are bundled and the logo embedded, so a generated report makes no network requests at all. It renders identically on an airgapped host and years later, when a hosted URL would have moved, and opening one no longer tells a third party that you did.
- **Reports are 23× smaller** — roughly 389 KB rather than 8.8 MB for a 182-finding scan. The previous logo was a 2.18 MB image displayed at 48 pixels and embedded three times.
- **New look.** Rebranded logo and a cyan-on-slate theme across light and dark. Every text colour now meets WCAG AA on both; three did not previously, one of them below the threshold even for large text.
- **Clearer report headings.** The same fact appeared as many as four times in the header of a report. Each now appears once, and the wording no longer assumes container images — findings are described by affected package, which holds for Maven, npm and other formats.
- Marked as production/stable.

### Fixed
- The update check reported an update whenever the local and published versions merely differed, so a build ahead of the published one advised "upgrading" to an older release. It now compares versions properly, can be disabled with `VULNLY_NO_UPDATE_CHECK`, and makes no network request when output is not attached to a terminal, so it is silent in CI.
- `--logo` described behaviour that no longer existed, and `--source` listed three of the four supported scanners.

### Removed
- The `output_path` argument to `_resolve_logo_html()` and `_resolve_footer_logo_html()`, left over from when logos were written beside the report rather than embedded. Only affects code calling those helpers directly; the command-line interface is unchanged.

## [1.0.0b9] - 2026-08-12

### Security
- Fixed stored cross-site scripting via scanner-supplied text. Advisory titles and package names come from vulnerability feeds and were not escaped before being written into the report, so crafted text could execute when the report was opened. A closing `</script>` sequence in a title also broke the report itself, leaving the vulnerability table empty.
- Fixed path traversal in advisory links. A crafted advisory identifier could point a row's link at an arbitrary page on `nvd.nist.gov` or `github.com` instead of the real advisory. Identifiers are now validated and fall back to an inactive link.

### Added
- Vulnerability table now loads 50 rows at a time with a **Load more** button, so reports with thousands of findings open quickly. Printing or exporting to PDF still includes every row.
- Reports carry a theme toggle in the header, so readers can switch between dark and light without regenerating. The choice is remembered per browser, and `--theme` still sets the theme a report opens in.
- Unknown-severity findings now appear in the severity cards, distribution chart, legend and filter buttons.

### Changed
- Report detail pills are simplified: Format, Scan Target and Package Size are removed, and Repository is renamed **Path** and shown only when it differs from the package name.

### Fixed
- Clean scans no longer report vulnerabilities. A report with no findings showed a red "Vulnerabilities Detected" banner and an action-required alert; it now shows a green no-vulnerabilities state.
- The "Immediate Action Required" alert now appears only when Critical or High severity findings are present. Scans with only Medium and Low findings show a review-recommended notice instead.
- Severity counts now reconcile. Unknown-severity findings were counted in the total but omitted from the severity cards and chart, so totals did not match the breakdown. A scan containing only unknown-severity findings also reported a total and then said no vulnerabilities were found.
- Snyk and Grype reports no longer show a blank scan date. Neither scanner records a scan timestamp, so reports read "Security scan completed on ." — they now fall back to the report date.
- Alert text no longer names severities with no findings, such as "0 Critical and 2 High".
- Reports no longer show duplicated paths such as `nginx/nginx` for image scans.
- Repository summaries no longer fail when a scanner reports a non-numeric package count.

### Removed
- The `TEMPLATE_FILE`, `TEMPLATES` and `REPO_SUMMARY_TEMPLATES` module attributes. Each report type now has a single theme-aware template. Code calling `generate_html()` or `generate_repo_summary_html()` directly should use `REPORT_TEMPLATE` / `REPO_SUMMARY_TEMPLATE` and pass `theme="light"` where a light report is wanted. The command-line interface is unaffected.

## [1.0.0b8] - 2026-04-12

### Added
- Snyk scanner support with auto-detection

## [1.0.0b4] - 2025-03-15

### Added

- Grype scanner support with auto-detection

## [1.0.0b3] - 2025-02-20

### Added

- Trivy scanner support with auto-detection

## [1.0.0b2] - 2025-01-25

### Added

- Dark theme HTML report template
- Cloudsmith scanner support

## [1.0.0b1] - 2025-01-10

### Added

- Initial beta release
- Self-contained HTML vulnerability report generation
- Cloudsmith JSON input support
- Zero external dependencies — uses only the Python standard library
