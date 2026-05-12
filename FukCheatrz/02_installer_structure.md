# 02 - Installer Structure

## Outer Container

The outermost file, A Installer.exe, is a self-extracting RAR archive. On execution it unpacks its payload to a temporary directory and launches the second-stage installer.

Extraction path confirmed by string:

```
%TEMP%\RarSFX0\
```

The SFX container format is standard WinRAR SFX. It is not a custom packer. The implication is that the full payload is not visible to a user inspecting the downloaded file without extracting it first.

## Second Stage

ExLoader_Installer.exe is a Flutter runner, identical in hash to ExLoader.exe. It exists as a separate binary to present the installation UI before writing files to their final location. It demands administrator privileges via its embedded application manifest before it will launch.

Manifest extracted from PE resources:

```xml
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
  <trustInfo xmlns="urn:schemas-microsoft-com:asm.v3">
    <security>
      <requestedPrivileges>
        <requestedExecutionLevel level="requireAdministrator" uiAccess="false"/>
      </requestedPrivileges>
    </security>
  </trustInfo>
  <application xmlns="urn:schemas-microsoft-com:asm.v3">
    <windowsSettings>
      <dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2017/WindowsSettings">
        PerMonitorV2
      </dpiAwareness>
    </windowsSettings>
  </application>
</assembly>
```

`requireAdministrator` means the operating system will block the process from launching entirely if the user declines the UAC prompt. There is no degraded mode or fallback. Elevation is a hard requirement. This is necessary because the installer writes to C:\Program Files\ExLoader\, a directory that requires administrative access, and because the application interacts with kernel-level system components that cannot be reached from a standard user context. Those interactions are documented in 07_kernel_and_driver.md.

`PerMonitorV2` DPI awareness indicates the installer was built with attention to display compatibility across different screen configurations. This is consistent with a polished consumer-facing product rather than a proof-of-concept or hobbyist build.

## Installation Target

Final installation path confirmed by string:

```
C:\Program Files\ExLoader\
```

After installation the second-stage runner exits and the primary ExLoader.exe is launched from the installed location.

## Process Architecture

The installed application runs as two concurrent processes:

```
ExLoader.exe       UI process, handles interface rendering and user interaction
vpnshumans.exe     Separate process launched from the same binary for network operations
```

vpnshumans.exe is byte-for-byte identical to ExLoader.exe. The name implies a VPN or network helper role. Running network operations in a separately named process means a user or analyst inspecting the running process list sees two unrelated-looking process names rather than one, and process-level monitoring tools that flag ExLoader.exe by name would not catch vpnshumans.exe without a separate rule.
