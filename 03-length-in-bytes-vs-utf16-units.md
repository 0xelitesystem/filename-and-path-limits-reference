# Length in bytes versus UTF-16 units

Linux and macOS headers count name length in bytes, while Windows and HFS+ count UTF-16 code units, so the same visible name can fit on one side and overflow on the other.

## The numbers, with their units

- ext4: "file names cannot be longer than 255 bytes". https://docs.kernel.org/filesystems/ext4/directory.html
- Linux: NAME_MAX 255 and PATH_MAX 4096, "including nul". The header comment says "chars", and in C a char is one byte. https://github.com/torvalds/linux/blob/08df884136f1c1197bab2a27814404fd329d9aac/include/uapi/linux/limits.h
- POSIX minimums, all in bytes: _POSIX_NAME_MAX 14, _POSIX_PATH_MAX 256, _XOPEN_NAME_MAX 255, _XOPEN_PATH_MAX 1024. XBD 3.254 says PATH_MAX counts the terminating null byte. https://pubs.opengroup.org/onlinepubs/9799919799/basedefs/limits.h.html
- macOS: NAME_MAX 255 "max bytes in a file name", PATH_MAX 1024 "max bytes in pathname", with the note "Many filesystems (including HFS & APFS) may support names longer than `NAME_MAX` bytes." https://github.com/apple-oss-distributions/xnu/blob/f6217f891ac0bb64f3d375211650a4c1ff8ca1ea/bsd/sys/syslimits.h
- HFS+: HFSUniStr255 is `UniChar unicode[255]` and "UniChar is a UInt16", so 255 UTF-16 code units. https://developer.apple.com/library/archive/technotes/tn/tn1150.html
- Windows: component length is the lpMaximumComponentLength value, "commonly 255" (https://learn.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation), which GetVolumeInformationW reports "in TCHARs" (https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-getvolumeinformationw): UTF-16 code units, which Microsoft's prose calls characters. Read it per volume.

UTF-8 uses 1 to 4 octets per character (RFC 3629, https://www.rfc-editor.org/rfc/rfc3629).

## Measure it yourself

```
python -c "s='\u00e9'; print(len(s.encode('utf-8')), len(s.encode('utf-16-le'))//2)"
```

| Character | UTF-8 bytes | UTF-16 units | Max in 255 bytes | Max in 255 units |
|---|---|---|---|---|
| U+00E9 é | 2 | 1 | 127 | 255 |
| U+4E2D | 3 | 1 | 85 | 255 |
| U+1F600 | 4 | 2 | 63 | 127 |
| e + U+0301 (NFD) | 3 | 2 | 85 | 127 |

A name of 200 CJK characters fits a 255-unit Windows component and is too long for ext4.
