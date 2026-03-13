# Bug Bounty Report Generator — HackerOne-Formatted Vulnerability Reports from Scan Output

> **Turn your Nuclei and Dalfox scan findings into professional, submission-ready bug bounty reports in seconds.**
>
> [![Python](https://img.shields.io/badge/Python-3.6%2B-blue?logo=python)](https://www.python.org/)
> [![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
> [![Dependencies](https://img.shields.io/badge/dependencies-none-brightgreen)](https://github.com/shuvonsec/bug-bounty-report-generator)
>
> Bug Bounty Report Generator is an open-source **automated bug bounty report generator** that parses Nuclei and Dalfox output and produces structured, HackerOne-ready Markdown reports. It includes built-in templates for 11+ vulnerability types — XSS, SSRF, IDOR, subdomain takeover, CORS, open redirect, auth bypass, info disclosure, and more — with zero external dependencies.
>
> ---
>
> ## Table of Contents
>
> - [Features](#features)
> - - [Requirements](#requirements)
>   - - [Supported Vulnerability Types](#supported-vulnerability-types)
>     - - [Usage](#usage)
>       - - [Output](#output)
>         - - [Scanner Compatibility](#scanner-compatibility)
>           - - [License](#license)
>            
>             - ---
>
> ## Features
>
> - **Batch mode** — process an entire `findings/` directory at once
> - - **Manual mode** — create a single report from the command line
>   - - **PoC screenshot attachment** — embed images directly in reports
>     - - **HackerOne-formatted output** — Markdown with severity, CVSS, CWE, and reproduction steps
>       - - **Zero dependencies** — pure Python stdlib, works everywhere
>         - - **SUMMARY.md table** — one-line overview of all findings
>           - - **INDEX.json manifest** — machine-readable index for automation
>            
>             - ---
>
> ## Requirements
>
> - Python 3.6+
> - - No external dependencies (stdlib only)
>  
>   - ---
>
> ## Supported Vulnerability Types
>
> | Type | Default Severity | CWE |
> |---|---|---|
> | `xss` | Medium | CWE-79 |
> | `ssrf` | High | CWE-918 |
> | `idor` | High | CWE-639 |
> | `takeover` | High | CWE-284 |
> | `cors` | Medium | CWE-942 |
> | `redirect` | Low | CWE-601 |
> | `exposure` | Medium | CWE-200 |
> | `cve` | High | CWE-1035 |
> | `misconfig` | Medium | CWE-16 |
> | `auth_bypass` | Critical | CWE-287 |
> | `info_disclosure` | High | CWE-200 |
>
> ---
>
> ## Usage
>
> ### Batch Mode — Process a Findings Directory
>
> ```bash
> python3 report_generator.py ./findings/target-name/
> ```
>
> The findings directory should contain subdirectories named by vulnerability type (`xss/`, `ssrf/`, `takeover/`, `idor/`, etc.) with `.txt` files of scanner output (one finding per line).
>
> ### Manual Mode — Create a Single Report
>
> ```bash
> # XSS report
> python3 report_generator.py --manual --type xss \
>   --url "https://example.com/search?q=test" --param q
>
> # SSRF report
> python3 report_generator.py --manual --type ssrf \
>   --url "https://example.com/fetch?url=http://169.254.169.254"
>
> # IDOR report
> python3 report_generator.py --manual --type idor \
>   --url "https://api.example.com/users/123" \
>   --evidence "Changed ID to 124, got another user's data"
> ```
>
> ### Attach PoC Screenshots
>
> ```bash
> python3 report_generator.py --manual --type xss \
>   --url "https://example.com/search?q=test" \
>   --poc-images screenshot1.png screenshot2.png
> ```
>
> ---
>
> ## Output
>
> Batch mode produces the following structure under `./reports/<target>/`:
>
> ```
> reports/target/
>   xss_<hash>.md         # Individual vulnerability report
>   ssrf_<hash>.md
>   ...
>   SUMMARY.md            # Table of all findings with severity
>   INDEX.json            # Machine-readable manifest
> ```
>
> Each report follows HackerOne's recommended format: title, severity, CVSS score, CWE, description, reproduction steps, impact, and remediation.
>
> ---
>
> ## Scanner Compatibility
>
> | Scanner | Parsed Format |
> |---|---|
> | **Nuclei** | `[template-id] [protocol] [severity] URL` |
> | **Dalfox** | XSS findings with `POC`/`Verified` markers |
>
> ---
>
> ## License
>
> [MIT](LICENSE) — built to make bug bounty reporting faster for everyone.
