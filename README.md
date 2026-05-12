# ExLoader Static Malware Analysis

**Sample:** A Installer.exe
**SHA256:** bf29ef463bbcf3860e94157aa93903864049d28d49f17edc0ccaf76ab88f0da8

---

## Background

ExLoader is a Windows game cheat loader built on Flutter Desktop by losers for losers, distributed by a developer operating under the name SwiftSoft. It presents as a free modification library for popular titles including CS:GO, Fortnite, and various Unity-based games. It is distributed across 22 language markets with localized UI and geo-targeted monetization.

Static analysis of the primary application binary (data/app.so, 21 MB of compiled Dart AOT code) and surrounding installer components reveals capabilities that extend well beyond what a cheat loader requires to function. The findings are documented here for responsible disclosure purposes.

All evidence in this repository is string-based, extracted directly from the binary without execution. Where a string's origin is ambiguous (for example, strings that may originate from vendored cryptography libraries), that ambiguity is noted in the relevant file.

Also i **HIGHLY** recommend using "Cutter" tool. It has helped me extremely a lot in this project and many others.(It reminds me of a "modernized ghidra")

---

## Repository Structure

| File | Contents |
|---|---|
| 01_sample_identity.md | Hashes, compiler metadata, binary type, language coverage |
| 02_installer_structure.md | SFX layout, manifest analysis, privilege escalation |
| 03_file_tree.md | Full installed file tree with component notes |
| 04_network_infrastructure.md | Extracted endpoints, domains, in-memory regex scrub patterns |
| 05_encryption_and_comms.md | Cryptographic primitives, key exchange, C2 encryption stack |
| 06_registry_manipulation.md | Full registry key inventory grouped by behavioral purpose |
| 07_kernel_and_driver.md | BYOVD indicators, Code Integrity targeting, Secure Boot and HVCI checks |
| 08_process_and_memory.md | ntdll patching, injection method strings, Mono/Unity targeting |
| 09_anti_forensics.md | Event log deletion, self-delete, trace cleanup scope and targets |
| 10_security_software_enum.md | Antivirus enumeration by registry path, Defender policy interaction |
| 11_hwid_spoofing.md | Hardware identifier access, MachineGuid, UI screen evidence |
| 12_string_scrubbing.md | Game memory string erasure logic, regex rules, partial match behavior |
| 13_subscription_monetization.md | Subscription lifecycle strings, payment flow, license enforcement |
| 14_bundleware_pua.md | Third-party offer system, geo-targeting logic, re-prompt timestamps |
| 15_telemetry_backend.md | Crash and launch telemetry, admin panel screens, mod report infrastructure |
| 16_ui_feature_map.md | Full AssetManifest.json breakdown mapped to inferred application features |
| 17_attribution_artifacts.md | Developer LAN IP leaked in binary, build artifacts, identity indicators |
| 18_risk_summary.md | Risk classification table by capability area |

---
Also, i will be giving a terminal dump(Aka my full terminal copy-pasted.)

## Summary of Key Findings

Kernel driver loading is implemented with explicit awareness of Microsoft's Vulnerable Driver Blocklist. The application reads and targets the Code Integrity configuration registry key and checks Hypervisor-Protected Code Integrity status before attempting driver operations.

ntdll.dll is patched in process memory at runtime to remove user-mode hooks placed by antivirus, EDR, and anti-cheat software. This blinds those products to subsequent API calls made by the application.

Active forensic cleanup covers Windows event logs, Background Activity Monitor records, UserAssist execution history, RunMRU, WinRAR archive history, Recycle Bin contents, temporary RAR extraction folders, and Nvidia-related files. A user-facing string confirms the intended scope: "Registry, ShellBugs, LastActivityView, Downloads, Recent, Documents, AppData, Archive History, Game Folder, Network traffic traces will be completely erased from all these places as if you had never even downloaded ExLoader."

Manual map DLL injection and Unity/Mono runtime injection are both implemented. Steam's active process registry entry is read to locate target game processes.

All traffic to the backend domain exloader.net is encrypted using a full NaCl/libsodium-style authenticated encryption stack (ChaCha20-Poly1305, Salsa20) with a client/server key exchange handshake on each connection.

Antivirus products are enumerated by name via their uninstall registry paths: Avast, AVG, Norton 360, and Avira are all explicitly targeted.

A developer LAN IP address (192.168.2.220) is hardcoded in the production binary, leaked from the build environment.(Dumbass)

---

## Scope and Limitations

This analysis is based entirely on static string extraction from the binary. No claims are made about runtime behavior, actual driver filenames, or network traffic beyond what the strings directly indicate. Some strings, particularly the NaCl cryptographic primitives, originate from vendored Dart packages and their presence does not by itself confirm they are exercised at runtime. All such cases are noted in the relevant files.
