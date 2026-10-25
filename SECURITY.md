# Security Policy

Vulnly generates security reports, and those reports are shared, archived and
opened by other people. A flaw here can turn a scan result into a way to attack
the person reading it, so vulnerability reports are taken seriously.

## Reporting a vulnerability

Report it privately through
[GitHub Security Advisories](https://github.com/fullstackspectrum/Vulnly/security/advisories/new).
This is the preferred route: it lets us discuss and fix the issue before any
detail is public.

If you cannot use advisories, [open an issue](https://github.com/fullstackspectrum/Vulnly/issues)
saying you have a security report and asking for a private channel — **without
the technical detail**. Issues are public the moment they are filed, so
anything reproducible in one is disclosed to everybody, including before a fix
exists. We will respond with somewhere private to send it.

When you do report privately, please include:

- what the flaw allows an attacker to do
- the scanner output or input needed to reproduce it, ideally a minimal file
- the Vulnly version (`vulnly --version`) and how you installed it
- whether you intend to disclose publicly, and on what timeline

You will get an acknowledgement within **3 working days**, and an assessment
with a fix or mitigation plan within **10 working days**. If a report is
declined we will explain why.

We will credit you in the release notes and the advisory unless you would
rather remain anonymous.

## Supported versions

Fixes are released against the latest published version. Older versions do not
receive backports — the tool has no runtime dependencies and upgrading is a
`pip install --upgrade vulnly`.

| Version | Supported |
| --- | --- |
| Latest release | Yes |
| Anything earlier | No — upgrade |

## What counts as a vulnerability

The most valuable reports concern **scanner output being treated as trusted**.
Advisory titles, package names, versions and identifiers all originate from
vulnerability feeds and registries, so Vulnly treats them as attacker
controlled. In scope:

- content from a scan file executing or injecting when a report is opened
  (script injection, HTML injection, event handlers, `javascript:` URLs)
- content from a scan file executing when an exported CSV is opened in a
  spreadsheet (formula injection)
- a crafted scan file causing a report to write outside its intended output
  path, or the CLI to read or write unintended files
- a report making a network request, which would leak the fact and time of
  reading to a third party — reports are intended to be fully offline
- a crafted scan file crashing the generator in a way that silently produces
  an incomplete or misleading report

Also in scope: anything that makes a report **understate risk** — findings
silently dropped, counts that disagree with the rows they summarise, or a
severity rendered lower than the scanner reported.

## Out of scope

- vulnerabilities in the scanners themselves (report those to Trivy, Grype,
  Snyk or Cloudsmith)
- the accuracy of vulnerability data, which Vulnly renders but does not produce
- running Vulnly against a scan file you already control, on your own machine,
  to affect only yourself
- outdated versions of the bundled Chart.js or Inter, absent a demonstrated
  impact on a generated report

## Security properties we maintain

These are covered by the test suite, and a regression in any of them is a bug
worth reporting:

- **Scanner text never becomes code.** Values are escaped when emitted into the
  report's data array and set with `textContent` when rendered, so markup in a
  CVE title displays as text.
- **Reports are fully offline.** A generated report loads nothing from the
  network; Chart.js, the font and all images are embedded.
- **Advisory links are validated.** Only well-formed CVE and GHSA identifiers
  produce a link; anything else is inert.
- **CSV exports are inert.** Fields that a spreadsheet would treat as a formula
  are prefixed so they are read as text.
- **The generator itself makes one network request**, a PyPI version check,
  which can be disabled with `VULNLY_NO_UPDATE_CHECK` and is skipped when
  stderr is not a terminal.
