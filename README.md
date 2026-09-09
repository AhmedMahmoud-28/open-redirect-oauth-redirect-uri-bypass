# Open Redirect & OAuth `redirect_uri` Bypass Vault

> A categorized, source-attributed collection of Open Redirect and OAuth `redirect_uri` validation bypass techniques — for bug bounty hunters, pentesters, and AppSec engineers.

[![Made for Bug Bounty](https://img.shields.io/badge/made%20for-bug%20bounty-red)]()
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen)]()
[![License: CC BY-SA 4.0](https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey)]()

## Why this repo exists

Most open redirect payload lists online are copy-pasted from each other with no context and no sources. This repo tries to do something different:

- Every technique explains **why it works**, not just the payload
- Every technique is **attributed** to where it came from (academic paper, book, CVE, HackerOne report, or research blog)
- Techniques are organized by **root cause**, not just alphabetically dumped
- Includes **real-world disclosed cases** (CVEs, HackerOne reports) — not just theory

## Structure

| Folder | Covers |
|---|---|
| [`oauth-redirect-uri/`](./oauth-redirect-uri) | Path confusion, parameter pollution, and other OAuth-specific `redirect_uri` bypasses |
| [`domain-confusion/`](./domain-confusion) | Userinfo tricks, subdomain tricks, nested-URL tricks, backslash confusion |
| [`encoding-bypasses/`](./encoding-bypasses) | Layered URL encoding, Unicode normalization, non-ASCII look-alikes |
| [`real-world-cases/`](./real-world-cases) | Disclosed CVEs and HackerOne reports with root-cause breakdowns |
| [`checklists/`](./checklists) | Practical step-by-step testing methodology |

## Quick Start

New here? Start with [`checklists/testing-methodology.md`](./checklists/testing-methodology.md) for the hunting workflow, then dip into whichever category folder matches what you're testing.

## Sources

This vault compiles and attributes techniques from:

- **Academic research:** ACSAC 2023 — *"OAuth 2.0 Redirect URI Validation Falls Short, Literally"* (Innocenti, Golinelli, Onarlioglu, Mirheidari, Crispo, Kirda)
- **Standards:** RFC 9700 — OAuth 2.0 Security Best Current Practice (IETF, Jan 2025)
- **Books:** *Bug Bounty Bootcamp* (Vickie Li, No Starch Press), *Real-World Bug Hunting* (Peter Yaworski, No Starch Press)
- **Research:** PortSwigger Research, HackTricks, OWASP
- **Disclosed reports:** Public HackerOne reports, CVE advisories

Every payload file links back to its source. If you use this repo, please keep the attributions intact — see [LICENSE](./LICENSE).

## Disclaimer

For authorized security testing only — bug bounty programs you're enrolled in, or engagements you're contracted for. Do not test systems you don't have explicit permission to test.

## Author

Maintained by **Ahmed** — security researcher (Bugcrowd Hall of Fame, application security testing).

## Contributing

Found a new bypass, a CVE this is missing, or a mistake? See [CONTRIBUTING.md](./CONTRIBUTING.md).
