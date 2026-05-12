# 06 - Registry Manipulation

## Source Confirmation

Registry interaction is a first-class, explicitly named feature of the application. The following Dart source file paths were extracted from app.so, confirming registry manipulation is purpose-built code and not incidental:

```
package:exloader/logic/windows_related/registry.dart
package:win32_registry/src/registry.dart
package:win32_registry/src/registry_key.dart
package:win32_registry/src/registry_value.dart
```

The win32_registry package is a published Dart package for Windows registry access. Its presence confirms the application uses a structured, maintained library for registry operations rather than raw Win32 API calls.

## User Activity and Execution History

These keys record what programs the user has executed and when. Windows uses them to populate recently used file lists, jump lists, and the Run dialog history. ExLoader deletes them to remove evidence of its own execution from the user's activity record.

```
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\FeatureUsage
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\AppListBackup
HKEY_CURRENT_USER\Software\Microsoft\Windows\ShellNoRoam\MUICache
```

UserAssist stores ROT13-encoded records of every GUI program the user has launched, along with a run count and last-run timestamp. It is one of the primary registry artifacts examined during forensic triage. ExLoader's deletion of this key removes its own execution record from that artifact. The presence of ROT13-obfuscated registry keys in the anti-forensic string list (documented separately in 09_anti_forensics.md) is consistent with ExLoader targeting UserAssist specifically, since UserAssist data is itself ROT13-encoded by Windows.

## Archive and Download History

These keys record WinRAR archive history, which would reveal that the SFX installer was extracted by the user.

```
HKEY_CURRENT_USER\Software\WinRAR\ArcHistory
HKEY_CURRENT_USER\Software\WinRAR\DialogEditHistory\ArcName
```

Deleting these keys removes evidence that the user opened and extracted the outer A Installer.exe SFX archive.

## Kernel and Code Integrity Configuration

These keys control whether Windows enforces driver signature requirements and whether Hypervisor-Protected Code Integrity is active. Their presence in the registry access list indicates the application reads and potentially modifies these values. Full context is in 07_kernel_and_driver.md.

```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\CI\Config
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\HypervisorEnforcedCodeIntegrity
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecureBoot\State
```

CI\Config is the registry location where Windows stores its Code Integrity enforcement configuration. SecureBoot\State is read to determine whether Secure Boot is enabled before driver operations are attempted.

## Background Activity Monitor

The BAM service records process execution times at the kernel level, independently of user-space activity logs. It is a forensic artifact that persists even when user-space logs are cleared.

```
HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\bam\State\UserSettings
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\bam\State\UserSettings
```

ExLoader targets both the current control set and ControlSet001 to ensure the BAM record is deleted regardless of which control set Windows is using.

## Windows Defender Policy

```
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows Defender
```

This key is where Group Policy stores Windows Defender configuration overrides. Writing to this key can disable real-time protection or add exclusion paths without going through the Windows Security UI. See also 10_security_software_enum.md.

## Steam Process Location

```
HKEY_CURRENT_USER\Software\Valve\Steam
HKEY_CURRENT_USER\Software\Valve\Steam\ActiveProcess
HKEY_LOCAL_MACHINE\SOFTWARE\Valve\Steam
HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Valve\Steam
```

Steam's ActiveProcess key contains the PID of the currently running Steam client and the path to the Steam installation. The application reads this to locate running Steam game processes for injection targeting. This is documented in 08_process_and_memory.md.

## Installed Software Enumeration

The application reads standard uninstall registry paths to detect whether specific security software is installed. This is documented in full in 10_security_software_enum.md.

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Steam
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Avast
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Avast Antivirus
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\AVG Antivirus
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Norton 360
HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\Avira Security_is1
HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Installer\Products
HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Installer\Dependencies
```

## Hardware Fingerprinting

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Cryptography
HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Cryptography
HKEY_CURRENT_USER\Software\AMD\HKIDs
HKEY_CURRENT_USER\Software\ATI\ACE\Settings\Graphics\PowerXpress\Px4.0\ProfilelessAppList
```

Microsoft\Cryptography contains the machine's MachineGuid, a system-wide unique identifier that is a primary component of hardware-based ban systems. Reading it is consistent with either license enforcement (binding a subscription to a machine) or HWID ban detection logic. AMD\HKIDs suggests GPU hardware identifier collection for the HWID spoofer feature documented in 11_hwid_spoofing.md.

## Browser Detection for Bundleware

```
HKEY_CURRENT_USER\Software\Opera Software:Last Stable Install Path
HKEY_CURRENT_USER\Software\Opera Software:Stable Last Running
```

These keys are read to determine whether Opera is already installed before presenting the Opera offer. This is part of the geo-targeted bundleware system documented in 14_bundleware_pua.md.

## AutoHotkey Detection

```
HKEY_CLASSES_ROOT\.ahk\ShellNew
HKEY_LOCAL_MACHINE\SOFTWARE\AutoHotkey
```

AutoHotkey is a scripting tool commonly used with game cheats for macro automation. The application detects whether it is installed, likely to present relevant features or guidance to users who already use it.
