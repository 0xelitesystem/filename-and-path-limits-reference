# The portable subset

The POSIX portable filename characters are necessary for a name that works everywhere, but they are not sufficient on their own.

## What POSIX allows

POSIX.1-2024 XBD 3.146 defines a filename as "1 to {NAME_MAX} bytes" that may not contain NUL or slash. Every other byte is legal, so Linux accepts `CON`, `a:b` and `report.` without complaint. XBD 3.265 then defines the portable set: A to Z, a to z, 0 to 9, period, underscore and hyphen-minus. The note to 3.264 adds that a name should not start with a hyphen-minus, because it can be read as a command line option.

Source: https://pubs.opengroup.org/onlinepubs/9799919799/basedefs/V1_chap03.html

## Why the portable set is not enough

`CON`, `aux.c`, `NUL.txt`, `report.` and the pair `README` and `readme` use only portable characters. Each one still fails: the first three are Windows device names (chapter 02), the trailing period is stripped by Win32 (chapter 02), and the pair collides on a case-insensitive volume (chapter 05). Both sides are right. POSIX allows the bytes, and Windows publishes its own rules for what it will refuse.

## The full rule for a generated name

1. Use only A to Z, a to z, 0 to 9, period, underscore and hyphen.
2. Never use `.` or `..` as a whole name.
3. Do not start with a hyphen.
4. Do not end with a period.
5. Strip the extension, compare without case, and reject CON, PRN, AUX, NUL, COM1 to COM9 and LPT1 to LPT9 (chapter 02 lists the superscript forms too).
6. Lowercase every name in a directory and confirm they are still unique.
7. Keep each component within 255. For portable ASCII names, bytes and UTF-16 units are the same number.
8. Keep the full path, as it will sit on the target machine, under Windows' MAX_PATH of 260 (chapter 04).

Restricting names to ASCII is what removes two whole problems: the byte versus unit gap in chapter 03 and the normalization collisions in chapter 05. Allow non-ASCII names only when you control every system that will ever read them.
