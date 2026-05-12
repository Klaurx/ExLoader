# 01 - Sample Identity

## Hashes

| File | SHA256 |
|---|---|
| A Installer.exe | bf29ef463bbcf3860e94157aa93903864049d28d49f17edc0ccaf76ab88f0da8 |
| ExLoader.exe | bf29ef463bbcf3860e94157aa93903864049d28d49f17edc0ccaf76ab88f0da8 |
| vpnshumans.exe | bf29ef463bbcf3860e94157aa93903864049d28d49f17edc0ccaf76ab88f0da8 |
| ExLoader_Installer.exe | bf29ef463bbcf3860e94157aa93903864049d28d49f17edc0ccaf76ab88f0da8 |

All four executables share an identical SHA256. ExLoader.exe, vpnshumans.exe, and ExLoader_Installer.exe are the same Flutter runner binary deployed under different filenames for different runtime roles. This is intentional. The binary is compiled once and reused across all process roles.

## Binary Metadata

| Field | Value |
|---|---|
| Type | PE32+ 64-bit Windows GUI executable |
| Architecture | x86-64 |
| Subsystem | Windows GUI |
| Compiler | MSVC Linker 14.50 |
| Compilation timestamp | Sun Apr 19 11:24:28 2026 |
| Timestamp offset evidence | UTC+3, consistent with Eastern Europe / Russia / Turkey |
| Installed path | C:\Program Files\ExLoader\ |
| Primary logic binary | data/app.so, Dart AOT compiled, 21 MB |

The compilation timestamp places this as a build produced less than four weeks before the analysis date. The UTC+3 offset is consistent with the developer's apparent geographic base, supported by the Telegram identity and the density of Russian-language strings throughout the binary.

## Framework

ExLoader.exe is a Flutter Desktop runner for Windows. Flutter compiles all application logic into a separate AOT binary, data/app.so, which contains the Dart application compiled to native x86-64 machine code. The runner itself at 204 KB is a thin host for the Flutter engine (flutter_windows.dll, 20 MB). Nearly all application behavior is encoded in app.so, which is the primary analytical target.

Flutter plugins registered in the runner, confirmed by import table and string extraction:

```
audioplayers_windows_plugin.dll
permission_handler_windows_plugin.dll
url_launcher_windows_plugin.dll
```

These handle audio playback for UI interaction sounds, system permission requests, and opening URLs in the default browser respectively. The url_launcher plugin is used in the bundleware offer flow to redirect users to third-party installer pages.

## Developer Identity

Developer identity was established through three independent string indicators extracted from app.so:

```
https://t.me/swiftsoft_support
package:exloader/logic/...
.exloader.net/end_user_license_agreement
```

The Telegram support handle, the Dart package namespace, and the backend domain all resolve to the same entity. SwiftSoft is the developer name. ExLoader is the product name. The EULA is hosted on the developer's own domain, confirming exloader.net as the primary backend.

## Language Coverage

The application ships localized UI strings and flag assets for 22 languages:

```
Arabic, Czech, German, English, Spanish, Filipino, French, Hindi,
Indonesian, Italian, Kazakh, Korean, Mongolian, Polish, Portuguese,
Romanian, Russian, Turkish, Ukrainian, Uzbek, Vietnamese, Chinese
```

22 flag image files were confirmed in flutter_assets/resources/flags/. This is a globally distributed product with localized interface text and IP-based geo-targeting for its monetization layer, documented in 14_bundleware_pua.md.
