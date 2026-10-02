# Case and normalization collisions

Two names that differ only in case or in Unicode normalization are two files on ext4 and may be one file elsewhere, so a tree that is valid on Linux can lose files on clone. For NFC and NFD themselves, see https://github.com/0xelitesystem/unicode-and-normalization-reference

## Windows and NTFS

The naming page says "Do not assume case sensitivity" and "NTFS supports POSIX semantics for case sensitivity but this is not the default behavior". Per-directory case sensitivity "began in Windows 10, build 17107" and is set with `fsutil.exe file setCaseSensitiveInfo <path> enable` (https://learn.microsoft.com/en-us/windows/wsl/case-sensitivity). For normalization, the max-path page says the file system treats names as "an opaque sequence of WCHARs", so NFC café and NFD café are two different names on NTFS.

## APFS

Apple's APFS FAQ, a Retired Document updated 2018-06-04, says APFS accepts only valid UTF-8 names, preserves case and normalization, is case-insensitive by default on macOS, and that "In macOS High Sierra, APFS is normalization-insensitive". That statement is scoped to High Sierra; this reference makes no claim about later releases. https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/APFS_Guide/FAQ/FAQ.html

## HFS+

TN1150 says HFS+ names are "fully decomposed and in canonical order" using Apple's own decomposition table, and Mac OS X 10.3 and later use decompositions "based on Unicode 3.2". HFS+ compares case-insensitively; HFSX is the case-sensitive variant. https://developer.apple.com/library/archive/technotes/tn/tn1150.html

## ext4 and POSIX

Names are bytes, so they are case-sensitive and normalization-sensitive.

## Git

From git's core.adoc (https://github.com/git/git/blob/fa7f9290efe2bd22dd736689597b474b93798e11/Documentation/config/core.adoc): clone and init probe and set `core.ignoreCase`; `core.precomposeUnicode` is "only used by Mac OS implementation of Git" and "reverts the unicode decomposition of filenames done by Mac OS"; `core.protectHFS` defaults to true on Mac OS and `core.protectNTFS` to true on Windows.

When clone checks out colliding names, unpack-trees.c (lines 408 to 410, same commit) warns: "the following paths have collided (e.g. case-sensitive paths on a case-insensitive filesystem) and only one from the same colliding group is in the working tree".
