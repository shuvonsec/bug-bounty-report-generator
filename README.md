# Bug Bounty Report Generator

Generate HackerOne-formatted bug bounty reports from scan findings. Parses nuclei and dalfox output, with built-in templates for XSS, SSRF, IDOR, subdomain takeover, CORS, open redirect, auth bypass, info disclosure, and more.

## Requirements

- Python 3.6+
- No external dependencies (stdlib only)

## Usage

### Batch mode — process a findings directory

```bash
python3 report_generator.py ./findings/target-name/
```

The findings directory should contain subdirectories named by vulnerability type (`xss/`, `ssrf/`, `takeover/`, `idor/`, etc.) with `.txt` files of scanner output (one finding per line).

Output: individual Markdown reports, a `SUMMARY.md` table, and an `INDEX.json` manifest, all written to `./reports/<target>/`.

### Manual mode — create a single report

```bash
python3 report_generator.py --manual --type xss --url "https://example.com/search?q=test" --param q
python3 report_generator.py --manual --type ssrf --url "https://example.com/fetch?url=http://169.254.169.254"
python3 report_generator.py --manual --type idor --url "https://api.example.com/users/123" --evidence "Changed ID to 124, got another user's data"
```

### Attach PoC screenshots

```bash
python3 report_generator.py --manual --type xss --url "https://example.com/search?q=test" --poc-images screenshot1.png screenshot2.png
```

## Supported Vulnerability Types

| Type | Default Severity | CWE |
|------|-----------------|-----|
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

## Scanner Compatibility

- **Nuclei** — parses `[template-id] [protocol] [severity] URL` format
- **Dalfox** — parses XSS findings with POC/Verified markers

## License

MIT
