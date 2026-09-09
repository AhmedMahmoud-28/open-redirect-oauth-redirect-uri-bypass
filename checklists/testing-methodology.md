# Testing Methodology — Open Redirect & OAuth `redirect_uri`

## 1. Recon — find candidate parameters

Fuzz for common redirect parameter names across the target:

```
checkout_url, continue, dest, destination, go, image_url, next, redir,
redirect_uri, redirect_url, redirect, return_path, return_to, return,
returnTo, rurl, target, url, view, RelayState, u, n, forward, forward_url,
callback_url, jump, jump_url, clickurl, originUrl, origin
```

Google dorks to speed up recon on a specific target:

```
site:target.com inurl:%3Dhttp
site:target.com inurl:%3D%2F
site:target.com inurl:redirect
site:target.com inurl:redirect_uri
site:target.com inurl:returnurl
site:target.com inurl:relaystate
```

Also check for **referer-based redirects** — pages that redirect back to whatever the `Referer` header says after login/logout, with no parameter at all. See [`domain-confusion/`](../domain-confusion) for structural tricks and note that referer-based redirects need no payload — just a hosted link.

## 2. Baseline

Send a fully external domain as the parameter value. Does it get rejected outright? If yes, you're dealing with some form of validation — move to step 3. If no, you likely have a plain open redirect already.

## 3. Work through technique categories in order

1. **Domain confusion** — userinfo tricks, nested/embedded URLs, combining tricks ([`domain-confusion/`](../domain-confusion))
2. **Path traversal** (if this is an OAuth `redirect_uri`) ([`oauth-redirect-uri/path-confusion-and-traversal.md`](../oauth-redirect-uri/path-confusion-and-traversal.md))
3. **Parameter pollution** ([`oauth-redirect-uri/parameter-pollution.md`](../oauth-redirect-uri/parameter-pollution.md))
4. **Layered encoding** ([`encoding-bypasses/layered-encoding.md`](../encoding-bypasses/layered-encoding.md))
5. **Unicode / non-ASCII** ([`encoding-bypasses/unicode-and-nonascii.md`](../encoding-bypasses/unicode-and-nonascii.md))
6. **Blacklist keyword bypasses** if a filter/WAF is present ([`encoding-bypasses/blacklist-and-autocorrect.md`](../encoding-bypasses/blacklist-and-autocorrect.md))

## 4. Determine what kind of validator you're facing

Ask these three questions as you test — the answers tell you which technique category is worth focusing on:

| Question | If the answer is... | Focus on |
|---|---|---|
| Exact match or prefix/substring match? | Substring | Nested-URL and combining tricks |
| Single decode or double decode? | Mismatch between validator and executor | Layered encoding |
| Who resolves the final URL — browser, proxy, or backend? | Different component than the validator | Path traversal, backslash confusion |

## 5. Always verify visually

Don't trust a 200/302 response alone. Walk the full flow in an actual browser and confirm the **final landing host** — not just that the payload was "accepted."

## 6. Escalate the impact

An open redirect alone is often low severity. Chain it into:

- OAuth `redirect_uri` to exfiltrate `access_token` / authorization `code`
- SSRF allowlist bypass (if the redirect can be reached from a server-side fetch)
- Referer-header token/session leakage on the external hop
- Higher-credibility phishing (initial link shows the trusted domain)
- DOM-based XSS if the redirect value reaches a JS sink (`window.location`, etc.)

See individual technique files for source attribution and deeper root-cause explanations.
