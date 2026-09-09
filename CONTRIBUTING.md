# Contributing

Contributions are welcome — especially new techniques, corrections, and additional real-world cases.

## What makes a good contribution here

This vault's whole point is being more useful than a raw payload dump. A good PR includes:

1. **The payload(s)**
2. **Root cause** — why it actually works, in your own words
3. **A source** — paper, book, CVE, HackerOne report, or research blog. If you found it yourself in a real engagement, say so (redact the target).

## Adding a new technique

1. Pick the right folder (`oauth-redirect-uri/`, `domain-confusion/`, `encoding-bypasses/`, or `real-world-cases/`)
2. Follow the existing file format: `## Root cause`, `## Payloads`, `## Source`
3. Add a link to your new file from the relevant section of the main `README.md` if it introduces a new category

## Reporting a mistake

Open an issue. Security content ages fast — a payload that worked in one framework version may not work in the next, and vice versa. Corrections matter as much as additions.

## Code of conduct

- Only document techniques for **authorized testing** (bug bounty scope, contracted pentests, or your own labs).
- Do not submit exploits targeting a specific, named, non-consenting organization's production systems.
- Be respectful in issues and PR reviews.
