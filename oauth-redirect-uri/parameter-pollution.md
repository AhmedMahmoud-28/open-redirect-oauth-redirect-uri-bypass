# HTTP Parameter Pollution on `redirect_uri`

## Root cause

Some stacks have a **validation layer** and an **execution layer** that don't read duplicate parameters the same way. One reads the first occurrence, the other reads the last (or vice versa) — send both a legitimate and malicious value in the same request.

## Payloads

```
?redirect_uri=https://target.com/oauth-callback&redirect_uri=https://evil.com
?redirect_uri=https://evil.com&redirect_uri=https://target.com/oauth-callback
```

## Why it works

Different frameworks/languages have different defaults for handling duplicate query parameters:

| Stack | Typical default |
|---|---|
| PHP (`$_GET`) | Last value wins |
| Node.js (`req.query`) | Returns an array of all values (framework-dependent handling downstream) |
| Java servlets | Usually first value |
| Nginx (as a reverse proxy in front of a different backend) | Passes through as-is — the mismatch happens between proxy and app, not within one component |

If your validator and your actual redirect-execution code disagree on which value "wins," you have a bypass.

## Testing tip

Always test parameter pollution in **both orders** (malicious-first and malicious-last) — you don't know in advance which layer picks which position.

## Source

Innocenti, T., Golinelli, M., Onarlioglu, K., Mirheidari, A., Crispo, B., Kirda, E. (2023). *"OAuth 2.0 Redirect URI Validation Falls Short, Literally."* ACSAC 2023.
