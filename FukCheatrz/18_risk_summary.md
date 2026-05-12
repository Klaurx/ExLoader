# 18 - Risk Summary

## Classification

ExLoader is classified as malicious tooling with PUA characteristics. The PUA classification applies to the bundleware monetization layer. The malicious tooling classification applies to the kernel driver loading, ntdll patching, process injection, and anti-forensic capabilities, which collectively go well beyond what a cheat loader requires to function and which would cause direct harm to the security posture of any system on which they execute.

## Risk Table

| Capability | Evidence basis | Severity |
|---|---|---|
| Kernel driver loading with BYOVD awareness | startDriver, VulnerableDriverBlocklistEnable, areCoreIsolatedAndDriversVulnerabilityCheckEnabled, CI\Config registry key access | Critical |
| ntdll in-memory patching to remove AV/EDR hooks | _keepPatchedNtDllBytes, _writeNewNtdllBytes, _protectMemoryRegionAndWriteBufferIntoIt | Critical |
| Active deletion of forensic artifacts | _deleteEventLog, _selfDelete, _deleteActivityRegistry, _deleteRot13Registry, BAM registry targeting, Application.evtx targeting, user-facing scope confirmation string | Critical |
| Manual map DLL injection into game processes | _insertImportFunctions, _relocateImage, Steam module manual map injection has failed error string | Critical |
| Code Integrity and HVCI configuration access | CI\Config and HypervisorEnforcedCodeIntegrity registry key presence, SecureBoot\State read | Critical |
| Unity/Mono runtime injection | _openMonoAssembly, _invokeMonoLibraryRoutines | High |
| HWID spoofing with kernel device access | bios.png, flashdrive.png, harddrive.png UI assets, MachineGuid registry access, \device\ kernel namespace, QueryDosDevice | High |
| Antivirus enumeration by name | Avast, AVG, Norton 360, Avira registry paths | High |
| Windows Defender policy key access | HKLM\SOFTWARE\Policies\Microsoft\Windows Defender | High |
| Encrypted C2 communications resistant to interception | ChaCha20-Poly1305, Salsa20, NaCl key exchange, PacketCryptoIsolate | High |
| Game memory string and network indicator scrubbing | _isProfanityPresent, _cleanupStrings, IP and URL regex patterns applied to process memory | High |
| PUA bundleware with geo-targeting and re-prompt logic | kLastOfferReactivationTimestamp keys, _getUserCountryWithDecimalIP, _isGoodGeoByList | Medium |
| Launch and crash telemetry to developer servers | _sendLaunchData, _sendCrashData, _sendExceptionToServer | Medium |
| CheatEngine 7.4 auto-download from remote source | Hardcoded GitHub release URL | Medium |
| Developer LAN IP leaked in production binary | 192.168.2.220 hardcoded in app.so | Low (attribution value) |

## Compounding Factors

Several of the critical findings interact in ways that compound their severity.

The ntdll patching runs before injection, meaning by the time code is injected into a game process, the AV/EDR hooks that would have detected the injection are already removed. The anti-forensic cleanup runs after the session, meaning the evidence that the patching and injection occurred is erased. The encrypted C2 channel means network-based detection has no visibility into what the application communicates to its backend. Together these three capabilities form a sequence that operates, executes, and erases evidence with limited opportunity for detection at each stage.

The kernel driver loading capability, if exercised against a system where the HVCI and driver vulnerability checks are disabled or defeatable, would give the application code executing at the kernel level, from which all user-space security controls are accessible.
