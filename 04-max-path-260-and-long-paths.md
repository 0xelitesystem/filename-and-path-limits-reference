# MAX_PATH 260 and long paths

MAX_PATH is 260 characters for the whole path including the terminating null, and getting past it takes either an opt-in on both the machine and the app, or the `\\?\` prefix.

## What 260 covers

Microsoft's example: the maximum path on drive D is "D:\some 256-character path string<NUL>". The page adds: "you may hit this limitation if you are cloning a git repo that has long file names into a folder that itself has a long name." Also:

- Relative paths "are always limited to a total of MAX_PATH characters".
- A directory name "cannot exceed MAX_PATH minus 12", room for an 8.3 name.
- File APIs convert "/" to "\", except with `\\?\`.

## Route 1: the opt-in (Windows 10, version 1607 and later)

"two conditions must be met": HKLM\SYSTEM\CurrentControlSet\Control\FileSystem LongPathsEnabled, REG_DWORD, set to 1, and the app manifest must declare:

```xml
<application xmlns="urn:schemas-microsoft-com:asm.v3">
  <windowsSettings xmlns:ws2="http://schemas.microsoft.com/SMI/2016/WindowsSettings">
    <ws2:longPathAware>true</ws2:longPathAware>
  </windowsSettings>
</application>
```

"enabling this registry setting will only affect applications that have been modified to take advantage of the new feature". The value is cached per process, and a reboot might be required. Group Policy: Computer Configuration > Administrative Templates > System > Filesystem > Enable Win32 long paths.

## Route 2: the `\\?\` prefix

It needs Unicode APIs and an absolute path, skips all normalization, and "Many but not all file I/O APIs support" it. The limit becomes 32,767, which is approximate because the prefix may be expanded at run time.

## .NET and Git

The .NET path page: ".NET Core and .NET 5+ handles long paths implicitly and doesn't perform a MAX_PATH check. The MAX_PATH check applies only to .NET Framework."

`core.longpaths` is a Git for Windows setting, not upstream git. Its doc: "Enable long path (> 260) support for builtin commands in Git for Windows. This is disabled by default", and Git for Windows says long paths are not supported by Windows Explorer, cmd.exe or its own tool chain.

Sources: https://learn.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation, https://learn.microsoft.com/en-us/windows/win32/fileio/naming-a-file, https://learn.microsoft.com/en-us/dotnet/standard/io/file-path-formats, https://github.com/git-for-windows/git/blob/9dc6e13469a1e0170e96585d33ce17b232abfb28/Documentation/config/core.adoc
