# 10 - Security Software Enumeration

## Overview

ExLoader enumerates installed security software by reading standard Windows uninstall registry paths. Each product is checked by its exact registry key name, meaning these are targeted lookups, not generic software inventory scans.

## Antivirus Products

Registry paths read during enumeration, extracted from app.so:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Avast
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Avast Antivirus
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\AVG Antivirus
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\Norton 360
HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\Avira Security_is1
```

Avast, AVG, Norton 360, and Avira are each checked by name. AVG and Avast share a parent company (Gen Digital) and their engines are closely related, suggesting both are checked because their detection capabilities overlap. The WOW6432Node path for Avira indicates the application checks for both 32-bit and 64-bit install records.

The purpose of this enumeration is twofold. First, knowing which AV product is installed allows the application to tailor its behavior to avoid that product's specific detection signatures. Second, the bundleware offer system (documented in 14_bundleware_pua.md) offers Avast as a third-party install, and detecting whether Avast is already installed allows the application to suppress a redundant offer.

## Windows Defender

```
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows Defender
```

This is the Group Policy registry path for Windows Defender configuration. Writing to this key can modify Defender's behavior, including disabling real-time protection or adding exclusion entries. Reading it reveals whether any existing Group Policy has already modified Defender's configuration.

The UI asset defender.png confirms a dedicated Defender-facing screen exists in the application. The screen likely guides the user through adding ExLoader to Defender's exclusion list or presents automated exclusion logic.

## Broader Software Inventory

```
HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Installer\Products
HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Installer\Dependencies
```

These two keys provide a full inventory of all MSI-installed software on the system. Reading them gives the application a complete picture of installed products beyond just the explicitly named AV tools, potentially informing behavior toward other security or analysis tools not individually targeted by name.
