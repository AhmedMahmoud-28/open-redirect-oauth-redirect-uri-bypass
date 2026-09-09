# Unicode Normalization & Non-ASCII Look-alikes

## Root cause

Browsers normalize certain Unicode characters into ASCII equivalents during host resolution (per IDNA / Unicode normalization rules). Validators frequently compare raw strings without applying the same normalization — so a character that looks harmless to the validator becomes a structurally significant character (`/`, `.`) by the time the browser resolves it.

## Payloads

```
https://attacker.com%ff.example.com
https://attacker.com⁄.example.com          (U+2044 fraction slash look-alike)
https://evil.c℀.example.com                (U+2100 -> normalizes toward "a/c")
http://a.com／X.b.com                       (U+FF0F fullwidth solidus normalizes to /)
```

## HostSplit research (Black Hat USA 2019)

Jonathan Birch (Microsoft) demonstrated that specific Unicode characters, once passed through standard Unicode normalization, resolve into ASCII structural characters like `/` or `.` — splitting what looked like one hostname into two different path/host components between the validator's view and the browser's view.

Example from the research:
```
https://evil.c℀.example.com
```
normalizes to:
```
https://evil.ca/c.example.com
```
— the validator may see a single suspicious-looking hostname string and either reject or approve it based on the wrong resolved meaning.

## Cyrillic / visually-similar characters

Beyond structural characters, visually-identical Cyrillic letters (e.g. а vs a, е vs e, о vs o) can be used for domain spoofing in phishing contexts layered on top of a confirmed open redirect — the victim sees a familiar-looking domain in the address bar preview, but it resolves to a different registered domain entirely.

## Source

- Jonathan Birch, Black Hat USA 2019 — *"HostSplit: Exploitable Antipatterns in Unicode Normalization"*
- Bug Bounty Bootcamp, ch. 7 (Vickie Li)
