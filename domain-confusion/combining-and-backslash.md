# Combining Techniques & Backslash Confusion

## Combining start+end allowlist bypass

Some validators require the URL to both **start with** and **end with** the trusted domain — stronger than a plain "contains" check.

```
https://target.com.evil.com/target.com
https://target.com@evil.com/target.com
```

The first `target.com` is read by the browser as a subdomain (case 1) or userinfo (case 2), not as the real domain — but it satisfies the "starts with" check. The trailing `/target.com` satisfies the "ends with" check. The real host in both cases is `evil.com`.

**Source:** Bug Bounty Bootcamp, ch. 7 (Vickie Li)

## Backslash parser confusion

```
https://trusted.example\@evil.example/
/\\evil.example
```

Some backends parse `\` as a literal, harmless path character during validation. Browsers normalize `\` to `/` before resolving the URL — which can shift the boundary between userinfo and host, or reinterpret an absolute vs. relative path.

**Source:** CakePHP Authentication advisory GHSA-hhpq-7wg4-36jm (2026); HackTricks

## Userinfo bracket confusion

```
https://[evil.com]@target.com/
```

Square brackets are normally reserved for IPv6 literal hosts (`https://[::1]/`). Some parsers (e.g. certain configurations of Spring's `UriComponentsBuilder`) mishandle a bracketed value placed in the userinfo position, disagreeing with the browser about which part is the real host.

**Source:** PortSwigger Research
