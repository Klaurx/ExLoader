# 09 - Anti-Forensic Capabilities

## Overview

ExLoader implements active forensic cleanup of its own execution traces. This is not incidental file cleanup on uninstall. The application deletes forensic artifacts during normal operation and exposes this capability to the user through a dedicated UI screen (trash.png, log.png in the asset manifest).

The scope of what is erased is confirmed directly by a user-facing string extracted from app.so:

```
Registry, ShellBugs, LastActivityView, Downloads, Recent, Documents,
AppData, Archive History, Game Folder, Network traffic traces will be
completely erased from all these places as if you had never even
downloaded ExLoader
```

This string is part of the application's UI copy, written to communicate the cleanup scope to the user. It confirms the anti-forensic feature is intentional, user-facing, and marketed as a benefit.

## Function Names

Extracted from app.so:

```
_deleteEventLog
_selfDelete
_cleanupFiles
_cleanupNetwork
_cleanupStrings
_cleanupTraces
_deleteActivityRegistry
_deleteAdvancedRegistry
_deleteAudioAndGamingStaffRegistry
_deleteNvidiaFile
_deleteRarFolders
_deleteRecycleBin
_deleteRegistryKeys
_deleteRot13Registry
```

Each of these is a distinct function responsible for a specific cleanup target. The granularity confirms this is purpose-built anti-forensic code with dedicated logic for each artifact category.

## Registry Targets

The registry keys deleted by the anti-forensic routines are documented in full in 06_registry_manipulation.md. The primary targets are:

UserAssist, which records every GUI application the user has launched with run counts and timestamps. RunMRU, which records commands entered in the Run dialog. The Background Activity Monitor service records, which log process execution times at the kernel level. WinRAR archive history, which would show the SFX installer was extracted. MUICache, which records display names of executed applications.

_deleteRot13Registry specifically targets ROT13-encoded registry entries. UserAssist data is stored by Windows in ROT13-encoded form. This function name confirms ExLoader decodes and targets UserAssist entries directly rather than deleting the entire key indiscriminately.

## Filesystem Targets

Extracted from app.so, filesystem-level cleanup targets:

```
%SYSTEMROOT%\System32\Winevt\Logs\Application.evtx
_deleteRarFolders
_deleteRecycleBin
_deleteNvidiaFile
_cleanupNetwork
```

Application.evtx is the Windows Application Event Log file. Deleting it removes any application-level events logged during ExLoader's operation. _deleteRarFolders removes the temporary extraction directories created by the SFX installer (%TEMP%\RarSFX0\ and similar). _deleteRecycleBin empties the Recycle Bin to remove any files moved there during cleanup. _deleteNvidiaFile targets a specific Nvidia-related file, likely a log or configuration file that records GPU activity.

_cleanupNetwork suggests network-level artifact cleanup, consistent with removing DNS cache entries, network connection history, or similar records that would indicate the application communicated with exloader.net.

## Self-Deletion

```
_selfDelete
```

The application is capable of deleting its own executable after execution. This is a standard persistence-avoidance technique. The binary removes itself from disk so that a post-incident filesystem scan does not find it. Combined with the registry and event log cleanup, a system that has run ExLoader and triggered the cleanup routines may show minimal forensic evidence of the application having been present.
