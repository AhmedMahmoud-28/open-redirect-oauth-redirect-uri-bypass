# GitLab — Incomplete Fix Reintroduces Duplicate-Slash Traversal

**Reports:** HackerOne #1725190 (follow-up), related to CVE-2022-2250
**Impact:** OAuth access-token exfiltration via chained redirect

## Background

An earlier fix (addressing CVE-2022-2250) stripped duplicated slashes from a decoded path before comparing it against the `redirect_uri` allowlist — closing off a class of path-confusion bypasses similar to the ones in [`oauth-redirect-uri/path-confusion-and-traversal.md`](../oauth-redirect-uri/path-confusion-and-traversal.md).

## Root cause of the bypass

The normalization (slash-stripping) ran at **one specific point** in the request pipeline. Some routes, however, **prepended a slash automatically after that normalization step had already run** — reintroducing the exact duplicate-slash condition the original fix had removed, just later in the pipeline.

## Why this is an important lesson

This is not a "new" technique in the payload sense — it's a reminder that **fixing a validation bug at only one layer of a multi-layer routing system is not sufficient.** If normalization happens before routing, but routing itself can reintroduce the unsafe pattern, the fix only closes the door for requests that don't take that particular route.

## Practical impact

Chained with an open redirect elsewhere in the application, this allowed real **OAuth access token exfiltration** — not just a theoretical redirect to an untrusted page.

## Testing checklist for this pattern

- After confirming a redirect/path-normalization fix is in place, test the **same payload class through every route/entry point** you can find, not just the one where the original bug was reported.
- Look specifically for routes that manipulate the path *after* the point where sanitization/normalization is supposed to have already happened (redirects, internal rewrites, framework-level route matching that re-adds slashes or segments).

## Source

HackerOne public report #1725190 (GitLab); GitLab issue tracker re: CVE-2022-2250 follow-up.
