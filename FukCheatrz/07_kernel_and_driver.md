# 07 - Kernel and Driver Interaction

## Overview

ExLoader implements kernel-level driver loading. The application checks the system's kernel security configuration before attempting driver operations, and the strings present indicate awareness of and interaction with multiple layers of Windows kernel protection. This is the highest-severity finding in this analysis.

## Driver Loading Strings

Extracted from app.so:

```
get:driverMode
get:kernel
type=kernel
startDriver
test-sign-driver
Failed to create driver service
Failed to start driver service
Failed to load mod extra driver
```

get:driverMode and type=kernel are getter strings from the application's Dart state model, indicating kernel mode is a selectable operational mode exposed to the user through the UI (confirmed by kernel.png in the asset manifest). startDriver is a function name for the driver loading operation. test-sign-driver indicates the application supports loading test-signed drivers, which are drivers signed with a test certificate rather than a trusted production certificate.

The three failure strings are error messages with complete error handling logic around them. This is not experimental or prototype code. The driver loading feature has user-visible error states, which means it was written to be shipped and used.

## BYOVD Awareness

Extracted from app.so:

```
VulnerableDriverBlocklistEnable
areCoreIsolatedAndDriversVulnerabilityCheckEnabled
```

VulnerableDriverBlocklistEnable is the name of the Microsoft Vulnerable Driver Blocklist feature, which prevents known-vulnerable drivers from being loaded on the system. The application references this feature by its exact configuration name. areCoreIsolatedAndDriversVulnerabilityCheckEnabled is a function or property name that checks whether both Core Isolation and the driver vulnerability check are active before proceeding with driver operations.

These strings confirm the application is aware of the Vulnerable Driver Blocklist and performs a pre-flight check against it. The check's purpose is to determine the security posture of the target system before attempting driver loading.

## Code Integrity and Secure Boot

Registry keys read or written in relation to driver loading (also documented in 06_registry_manipulation.md):

```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\CI\Config
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\DeviceGuard\Scenarios\HypervisorEnforcedCodeIntegrity
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecureBoot\State
```

CI\Config is the registry location for Windows Code Integrity configuration. Windows uses this to enforce driver signature requirements. SecureBoot\State is read to check whether Secure Boot is enabled, which affects whether unsigned or improperly signed code can execute at the kernel level. The HypervisorEnforcedCodeIntegrity key relates to HVCI, a virtualization-based security feature that moves Code Integrity verification into a hypervisor-protected environment, making it significantly harder to bypass.

The application reads the Secure Boot and HVCI states as part of its pre-flight assessment. The CI\Config key being present in the application's registry access list indicates it is not only read but potentially written to as part of the driver loading sequence.

## Kernel Device Namespace

Extracted from app.so:

```
\device\
QueryDosDevice
```

The \device\ prefix is the Windows kernel device namespace, used to reference kernel objects such as physical drives and device interfaces directly, bypassing the filesystem abstraction layer. QueryDosDevice is a Win32 API function that resolves DOS device names (such as C:) to their underlying kernel device paths (such as \Device\HarddiskVolume3). Access at this level is consistent with the HWID spoofing feature documented in 11_hwid_spoofing.md, which targets physical storage device identifiers.
