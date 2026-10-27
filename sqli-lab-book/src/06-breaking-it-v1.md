# Breaking It V1

## Bypass it (maybe that was a bad fix)

Try obfuscating the `OR`. For example:

`' oR '1'='1 `

## Further reasons why that is a bad fix
- **There are infinite payloads.** An attacker can find *any* variation to bypass a blacklist.
- **False positives:** If a legitimate user had `or` in their password (uncommon, but possible), they would be blocked.
- **Obfuscation:** Comments `/**/`, encoding, or different syntax can all be used to bypass it.

This is security through obscurity. It is trivial to get around with enough effort.

## Undo your changes
Before moving onto the correct fix, revert `app.py` back to its original vulnerable state (the one from Chapter 2) or just remove the "fixes" that we just added. We will be replacing the logic entirely in the next chapter.
