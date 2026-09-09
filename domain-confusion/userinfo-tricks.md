# Userinfo (`@`) Parser Confusion

## Root cause

A URL can carry credentials before the host: `scheme://user:pass@host/path`. A validator that does a naive `.startsWith()` or `.includes()` check on the trusted domain can be fooled into "seeing" the trusted domain, while the browser correctly treats it as userinfo and navigates to whatever comes **after** the `@`.

## Basic payloads

```
https://target.com@evil.com
https://target.com%40evil.com
https://user:pass@evil.com
```

## Variants

```
http://academy.htb@attacker.com/callback
https://attacker.com\@academy.htb
https://attacker.com?@academy.htb
https://gitlab.com@123.123@evil.com
https://gitlab.com/<project>/@@evil.com
```

### `attacker.com\@academy.htb`
Some backends treat the backslash as a literal path character during validation (so the check may see a suspicious-but-non-matching string). Browsers normalize `\` to `/` first, which changes which segment becomes userinfo vs. host. Test both directions — the outcome depends on which component (validator vs. browser) does the normalizing.

### `attacker.com?@academy.htb`
Uses `?` instead of `@` as the visual separator. Some parsers treat everything after `?` as a query string belonging to `attacker.com`, while a naive substring check just sees `academy.htb` present somewhere and passes it.

### Repeated `@` (real HackerOne case)
`https://gitlab.com@123.123@evil.com` bypassed a fix that only checked the **first** `@` in the string. The browser resolves based on the **last** `@` before the host — always test with multiple `@` characters even after a fix has been applied for the single-`@` case.

## Source

- PayloadsAllTheThings — Open Redirect
- HackerOne report #1613430 (GitLab) — real disclosed case with bounty
- Bug Bounty Bootcamp, ch. 7 (Vickie Li)
