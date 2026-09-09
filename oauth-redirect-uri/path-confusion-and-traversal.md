# Path Confusion & Path Traversal in `redirect_uri`

## Root cause

The authorization server validates `redirect_uri` as a **raw string** (usually a prefix/startswith check against a registered value), but the value is later **resolved as a path** by the browser or the client application's own routing. These two steps can disagree about where the URL actually points.

## Payloads

```
/oauth-callback/../evil-endpoint
/oauth-callback/..%2Fevil-endpoint
/oauth-callback/..%252Fevil-endpoint
/oauth-callback/%2e%2e%2Fevil-endpoint
/oauth-callback/%252e%252e%252Fevil-endpoint
/oauth-callback/..\evil-endpoint
/oauth-callback//evil-endpoint
/oauth-callback/..//evil-endpoint
```

## Why the encoding variants matter

- Single traversal (`/../`) fails if the validator normalizes the path before comparing.
- Single-encoded (`%2F`) can survive a validator that checks the raw string but fails once *something* in the stack decodes it once.
- Double-encoded (`%252F`) targets stacks where **two separate components** each decode once — e.g., a reverse proxy decodes once, the backend decodes again.

## How to find your traversal depth

Count path segments in the registered callback and match `/../` occurrences 1:1:

```
https://target.com/oauth-callback              -> 1 segment  -> 1x ../
https://target.com/auth/callback/handler        -> 3 segments -> 3x ../ to reach root
```

## Verification step

Don't trust the validator's HTTP response alone — walk the full flow in an actual browser and confirm the final landing page visually. A payload that "passes validation" but resolves to a 404 isn't useful; you need it to resolve to a page you can actually leverage (ideally an existing open redirect on the client app).

## Empirical results

The ACSAC 2023 paper tested 20 path confusion payload variants against 16 popular Identity Providers. **6 of 16 were vulnerable**, including Facebook, Microsoft, GitHub, Atlassian, NAVER, and VK.

## Source

Innocenti, T., Golinelli, M., Onarlioglu, K., Mirheidari, A., Crispo, B., Kirda, E. (2023). *"OAuth 2.0 Redirect URI Validation Falls Short, Literally."* ACSAC 2023. https://doi.org/10.1145/3627106.3627140
