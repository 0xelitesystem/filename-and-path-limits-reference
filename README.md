# Filename and Path Limits Reference

Which characters, names and lengths Windows, macOS and Linux each refuse, what MAX_PATH 260 really limits, and why a name legal on one system fails in the zip, the build or the git clone on another.

A plain-language reference for anyone who generates filenames in code: export jobs, build outputs, uploads, archives and repositories. Every rule here is quoted from the primary source that defines it (POSIX, Microsoft Learn, Apple's technical documentation, the Linux and xnu headers, git and Git for Windows, and PKWARE's APPNOTE), with the URL beside it. Where two sources disagree, both are shown.

## The core idea

A generated filename survives Windows, macOS and Linux only if it uses the POSIX portable characters A to Z, a to z, 0 to 9, period, underscore and hyphen, is not a Windows device name such as CON or NUL with or without an extension, does not end in a period, stays unique when case is ignored, and keeps the full path under Windows' 260 limit. Each system follows its own published rule: POSIX allows every byte except NUL and slash, Win32 reserves device names and strips trailing periods, ext4 counts bytes while NTFS counts UTF-16 units. None of them is wrong. The failure appears only at the handoff, when a name made on one system is unpacked, built or cloned on another.

## What is inside

- [01-the-portable-subset.md](01-the-portable-subset.md) why the POSIX portable set is necessary but not sufficient, and the full eight-part rule.
- [02-windows-reserved-characters-and-device-names.md](02-windows-reserved-characters-and-device-names.md) the nine characters, the 28 device names, trailing periods, and the Windows 11 qualification.
- [03-length-in-bytes-vs-utf16-units.md](03-length-in-bytes-vs-utf16-units.md) every length limit with its unit, plus worked examples you can rerun.
- [04-max-path-260-and-long-paths.md](04-max-path-260-and-long-paths.md) what 260 counts, the two-condition opt-in, the `\\?\` route, and core.longpaths.
- [05-case-and-normalization-collisions.md](05-case-and-normalization-collisions.md) NTFS, APFS, HFS+, ext4 and git when two names look the same.
- [06-names-inside-zip-archives.md](06-names-inside-zip-archives.md) what APPNOTE requires of entry names and the UTF-8 flag.

## How to use it

- **Writing a slug or export filename function:** implement the rule in chapter 01 and test it against `CON`, `aux.c`, `report.` and the pair `README` and `readme`.
- **A Windows user cannot clone your repo:** check chapter 05 for case collisions and chapter 04 for path length, then measure your longest path plus a typical clone folder.
- **Non-English names fail on Linux but not Windows:** chapter 03 shows how to count bytes and units for the same name.
- **Building a zip for download:** chapter 06 is the checklist to run before the archive is written.

## The stance

Independent reference, not affiliated with Microsoft, Apple, The Open Group, the Linux kernel project, the Git project or PKWARE. Sources were fetched and the quoted text checked on 2026-10-01. Microsoft Learn pages are edited in place, so recheck a quote before relying on its exact wording. Where a behaviour could not be verified against a primary source, it was left out.

## License

MIT. Copyright 0xelitesystem 2026.
