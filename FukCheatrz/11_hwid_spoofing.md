# 11 - Hardware ID Spoofing

## Overview

ExLoader integrates a hardware identifier spoofer. The feature is user-facing, with dedicated UI screens confirmed by asset manifest icons, and the application reads hardware-identifying registry keys and accesses kernel device paths consistent with low-level hardware enumeration.

## User-Facing Evidence

A string extracted from app.so, addressed directly to the user:

```
do not forget to use Spoofer to avoid HWID bans!
```

This is UI copy. It confirms the spoofer is a promoted feature, not a hidden capability. The application actively reminds users to use it.

UI icons from AssetManifest.json that map to spoofer screens:

```
bios.png
flashdrive.png
harddrive.png
```

Each icon corresponds to a distinct hardware component targeted by the spoofer: system firmware identifiers (BIOS), removable storage, and fixed storage respectively. Three separate screens suggest the spoofer operates at the component level rather than applying a single system-wide identifier override.

## Registry Access for Hardware Identification

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Cryptography
HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Cryptography
```

The MachineGuid value stored under Microsoft\Cryptography is a system-wide unique identifier generated at Windows installation time. It is one of the primary values used by game anti-cheat systems to construct a hardware fingerprint. Reading it is a prerequisite for either spoofing it or for comparing the current value against a stored baseline.

```
HKEY_CURRENT_USER\Software\AMD\HKIDs
```

AMD\HKIDs stores hardware identifiers associated with AMD GPU hardware. Its presence in the registry access list indicates the spoofer targets GPU identifiers in addition to system-level identifiers.

## Kernel Device Access

Extracted from app.so:

```
\device\
QueryDosDevice
```

The \device\ namespace is how Windows kernel objects, including physical storage devices, are addressed at the driver level. Access at this level, combined with the harddrive.png and flashdrive.png UI assets, indicates the spoofer reads and potentially modifies storage device serial numbers or identifiers through the kernel device interface rather than through higher-level Windows APIs.

QueryDosDevice maps a DOS drive letter to its underlying kernel device path, which is the prerequisite step before accessing a storage device through the kernel namespace.
