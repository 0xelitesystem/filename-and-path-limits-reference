# Names inside zip archives

A zip entry name only has to follow the zip format's rules, not the rules of the machine that extracts it, so the generator has to apply the chapter 01 rule before archiving.

Source for everything below: PKWARE APPNOTE 6.3.10, revised Nov 01, 2022, https://pkwaredownloads.blob.core.windows.net/pem/APPNOTE.txt

## What the format requires of a path

Section 4.4.17.1: the stored path "MUST NOT contain a drive or device letter, or a leading slash", and "All slashes MUST be forward slashes". A Windows path written as `docs\a.txt` breaks that rule, so convert separators before writing.

## How the name is encoded

- General purpose bit 11, the Language encoding flag (EFS): if set, the filename "MUST be encoded using UTF-8".
- Appendix D.1: the format historically supported only IBM Code Page 437.
- D.2: if bit 11 is unset, the name "SHOULD conform to the original ZIP character encoding".
- D.6 and D.7: Info-ZIP defined extra field records to carry a UTF-8 name separately; the Info-ZIP Unicode Path Extra Field is 0x7075.
- D.8: the choice of method is left to the writer, and readers SHOULD expect either one.

The practical consequence: a UTF-8 name like `café.txt` written with bit 11 unset and no 0x7075 field does not match what D.2 tells a reader to expect. Set bit 11 when you write UTF-8 names.

## Both sides are right

Nothing in 4.4.17.1 forbids an entry called `CON`, `a?b` or `report.`, or a path 300 UTF-16 units long. The archive is valid. The extracting machine then applies its own rules (chapters 02 and 04) and cannot create the file. Neither side is broken; the failure appears at the handoff.

## What to do before you archive

1. Run every entry name through the chapter 01 rule.
2. Write forward slashes only, with no drive letter and no leading slash.
3. Lowercase all entry paths and check that they are still unique.
4. Add the longest entry path to the deepest folder a user is likely to extract into, and keep the total under 260.
5. Set bit 11 if any name contains a non-ASCII character.
