# Bug Bounty Report Generator — HackerOne-Formatted Vulnerability Reports from Scan Output

**Turn your Nuclei and Dalfox scan findings into professional, submission-ready bug bounty reports in seconds.**

[![Python](https://img.shields.io/badge/Python-3.6%2B-blue?logo=python)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Dependencies](https://img.shields.io/badge/dependencies-none-brightgreen)](https://github.com/shuvonsec/bug-bounty-report-generator)

Bug Bounty Report Generator is an open-source **automated bug bounty report generator** that parses Nuclei and Dalfox output and produces structured, HackerOne-ready Markdown reports. It includes built-in templates for 11 vulnerability types — XSS, SSRF, IDOR, subdomain takeover, CORS, open redirect, auth bypass, info disclosure, and more — with zero external dependencies.

---

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Supported Vulnerability Types](#supported-vulnerability-types)
- [Usage](#usage)
- [Output](#output)
- [Scanner Compatibility](#scanner-compatibility)
- [License](#license)

---

## Features

- **Batch mode** — process an entire `findings/` directory at once
- **Manual mode** — create a single report from the command line
- **PoC screenshot attachment** — embed images directly in reports
- **HackerOne-formatted output** — Markdown with severity, CVSS, CWE, and reproduction steps
- **Zero dependencies** — pure Python stdlib, works everywhere
- **SUMMARY.md table** — one-line overview of all findings
- **INDEX.json manifest** — machine-readable index for automation

---

## Requirements

- Python 3.6+
- No external dependencies (stdlib only)

---

## Supported Vulnerability Types

| Type | Default Severity | CWE |
|---|---|---|
| `xss` | Medium | CWE-79 |
| `ssrf` | High | CWE-918 |
| `idor` | High | CWE-639 |
| `takeover` | High | CWE-284 |
| `cors` | Medium | CWE-942 |
| `redirect` | Low | CWE-601 |
| `exposure` | Medium | CWE-200 |
| `cve` | High | CWE-1035 |
| `misconfig` | Medium | CWE-16 |
| `auth_bypass` | Critical | CWE-287 |
| `info_disclosure` | High | CWE-200 |

---

## Usage

### Batch Mode — Process a Findings Directory

```bash
python3 report_generator.py ./findings/target-name/
```

The findings directory holds one subdirectory per vulnerability type, each containing `.txt` files of scanner output with one finding per line. Batch mode reads exactly these directory names:

| Directory | Report type |
|---|---|
| `xss/` | xss |
| `ssrf/` | ssrf |
| `idor/` | idor |
| `takeover/` | takeover |
| `cors/` | cors |
| `cves/` | cve |
| `redirects/` | redirect |
| `exposure/` | exposure |
| `misconfig/` | misconfig |
| `auth_bypass/` | auth_bypass |
| `info_disclosure/` | info_disclosure |

Note that `cves/` and `redirects/` are plural here, while the matching `--type` values in manual mode are singular (`cve`, `redirect`). Any directory name not in the table above is skipped silently, so a typo produces an empty run rather than an error.

Three more rules worth knowing:

- Only `.txt` files are read, and files with `manual` in the name are skipped — so you can keep hand-written notes next to scanner output without them being turned into reports.
- Every line needs an `http://` or `https://` URL in it. Lines without one are dropped, which means a bare list of hostnames produces nothing.
- The parser is picked by filename, not contents: a name containing `dalfox` goes through the Dalfox parser, everything else through the Nuclei parser. Dalfox output saved as `xss.txt` still parses, but you lose the `POC`/`Verified` check that promotes a finding to high severity.

A layout that works end to end:

```
findings/example-com/
  xss/dalfox.txt
  ssrf/nuclei.txt
  cves/nuclei.txt
  redirects/nuclei.txt
```

### Manual Mode — Create a Single Report

```bash
# XSS report
python3 report_generator.py --manual --type xss \
  --url "https://example.com/search?q=test" --param q

# SSRF report
python3 report_generator.py --manual --type ssrf \
  --url "https://example.com/fetch?url=http://169.254.169.254"

# IDOR report
python3 report_generator.py --manual --type idor \
  --url "https://api.example.com/users/123" \
  --evidence "Changed ID to 124, got another user's data"
```

### Attach PoC Screenshots

```bash
python3 report_generator.py --manual --type xss \
  --url "https://example.com/search?q=test" \
  --poc-images screenshot1.png screenshot2.png
```

---

## Output

Reports are written to a `reports/` directory **alongside the clone**, in the parent of the directory holding `report_generator.py`. This path is absolute, so it does not follow your current working directory:

```
~/tools/bug-bounty-report-generator/report_generator.py   # the script
~/tools/reports/<target>/                                 # where reports land
```

Keeping output outside the repo is deliberate — generated reports never show up in `git status`. The destination is printed at the end of every batch run if you are unsure where yours went.

Batch mode produces:

```
reports/<target>/
  xss_001.md            # Individual vulnerability report
  ssrf_001.md
  ...
  SUMMARY.md            # Table of all findings, sorted by severity
  INDEX.json            # Machine-readable manifest
```

Each report follows HackerOne's recommended format: title, severity, CVSS score, CWE, description, reproduction steps, impact, and remediation.

To write somewhere fixed instead, set `REPORTS_DIR` near the top of `report_generator.py` to an absolute path:

```python
REPORTS_DIR = os.path.expanduser("~/bounty-reports")
```

Nothing else depends on `BASE_DIR`, so that is the whole change.

---

## Scanner Compatibility

| Scanner | Parsed Format |
|---|---|
| **Nuclei** | `[template-id] [protocol] [severity] URL` |
| **Dalfox** | XSS findings with `POC`/`Verified` markers |

---

## License

[MIT](LICENSE) — built to make bug bounty reporting faster for everyone.
