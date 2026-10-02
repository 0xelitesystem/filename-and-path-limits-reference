# Windows reserved characters and device names

Windows refuses nine printable characters, the control characters and 28 device names, and silently strips a trailing space or period from a name.

## Characters

Microsoft's naming page lists `< > : " / \ | ? *`, the "Integer value zero", and "Characters whose integer representations are in the range from 1 through 31, except for alternate data streams where these characters are allowed."

## Device names

The 28 reserved names are CON, PRN, AUX, NUL, COM1 to COM9, COM¹, COM², COM³, LPT1 to LPT9, LPT¹, LPT² and LPT³. The page notes that Windows treats the ISO/IEC 8859-1 superscript digits as digits, so these are "reserved in every directory. For example, echo test > COM¹ fails to create a file."

On extensions, the naming page says: "Also avoid these names followed immediately by an extension; for example, NUL.txt and NUL.tar.gz are both equivalent to NUL." Microsoft's .NET path page, under "Handle legacy devices", qualifies that by version: "Prior to Windows 11, a path that begins with a legacy device name is always interpreted as a legacy device by the Path.GetFullPath(String) method. For example, the DOS device path for CON.TXT is \\.\CON ... Because this no longer applies with Windows 11". The advice that holds on every version: avoid these names with or without an extension.

## Trailing spaces and periods

"Do not end a file or directory name with a space or a period", although "it is acceptable to specify a period as the first character of a name." The .NET page explains the mechanism: "If a segment ends in a single period, that period is removed." and "If the path doesn't end in a separator, all trailing periods and spaces (U+0020) are removed." A path that starts exactly with `\\?\` skips this normalization, which is why a file named `hidden.` "is impossible to access in any other way".

Both sides are right: ext4 stores `report.` exactly as written, and Win32 reads the same name back as `report`.

Sources: https://learn.microsoft.com/en-us/windows/win32/fileio/naming-a-file and https://learn.microsoft.com/en-us/dotnet/standard/io/file-path-formats
