# Nested/Embedded Trusted-URL & Scheme Tricks

## Nested trusted domain in query string or fragment

### Root cause
Validators that check whether the trusted domain **appears anywhere** in the URL (instead of checking the actual host component) are defeated by putting the trusted domain somewhere harmless — a query value or a fragment — while the real host is the attacker's.

### Payloads
```
http://attacker.com/callback?a=http://academy.htb
http://attacker.com/callback#http://academy.htb
```

The real host in both cases is `attacker.com`. `academy.htb` is inert text sitting in the query string or fragment. A naive "contains academy.htb" check passes both.

### Why the fragment variant matters for OAuth specifically
In implicit-grant OAuth flows, the `access_token` itself lands in the URL fragment after redirection. A `redirect_uri` validator that regex-matches loosely against the whole URL string can be fooled by a fake trusted-domain fragment sitting right next to where the real token will later be appended by the authorization server.

## Double-colon scheme confusion

### Payload
```
http:://academy.htb.attacker.com/callback
```

The extra colon after the scheme is invalid per RFC 3986, but some URL parsers silently recover from it while others handle the malformed scheme differently — combined here with a subdomain trick (`academy.htb.attacker.com`). Worth testing against any parser that appears to do lenient scheme handling, since graceful recovery from the malformed scheme may resolve the host differently than the validator expects.

## CRLF-based domain confusion

### Payload
```
attacker.com%0d%0aacademy.htb
```

Carriage-return/line-feed injected between two host values. Depending on how the receiving component splits headers/lines internally, this can smuggle a second value past a validator that only inspects the first line, or inject an extra header-like line that a downstream component picks up instead of the intended one.

## Source

Compiled and analyzed for this vault; underlying pattern (host vs. query/fragment confusion) is a known class described across PortSwigger Research and HackTricks.
