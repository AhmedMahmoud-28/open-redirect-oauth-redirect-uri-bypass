# Layered URL Encoding

## Root cause

A validator and the component that actually resolves the URL (browser, proxy, or backend) may decode the string a **different number of times** before use. If the validator decodes once but the browser's rendering engine effectively "sees" a second decode happen somewhere in the pipeline, an encoded payload can slip through as harmless-looking, then reveal its real target after the extra decode.

## Payloads

```
https://example.com%2f@attacker.com
https://example.com%252f@attacker.com
https://example.com%25252f@attacker.com
https://attacker.com%252f@example.com
```

`%2f` is an encoded `/`. `%252f` is `/` encoded twice (`%25` is an encoded `%`). Each extra layer targets a stack with one more decode step between validation and execution.

## Testing approach

1. Try zero, one, and two layers of encoding on any payload that gets rejected in its raw form.
2. Test the encoded segment in **both** positions — before and after the `@` — since you don't know in advance which side the validator inspects most carefully.
3. If double-encoding succeeds where single-encoding fails, that's a strong signal there are two independent components in the request path (e.g. a reverse proxy and the application server) that each perform one decode.

## Related: path traversal encoding layers

The same principle applies to `/../` traversal sequences used against OAuth `redirect_uri` — see [`oauth-redirect-uri/path-confusion-and-traversal.md`](../oauth-redirect-uri/path-confusion-and-traversal.md).

## Source

Bug Bounty Bootcamp, ch. 7 (Vickie Li)
