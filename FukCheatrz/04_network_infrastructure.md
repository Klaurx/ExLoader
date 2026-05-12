# 04 - Network Infrastructure

## Confirmed Endpoints

All endpoints extracted from string analysis of app.so.

| Endpoint | Purpose |
|---|---|
| exloader.net | Primary backend domain, confirmed via EULA path string |
| .exloader.net/end_user_license_agreement | EULA hosted on developer's own infrastructure |
| https://t.me/ExLoader | Official Telegram channel |
| https://t.me/exloader_chat | Community and support chat |
| https://t.me/swiftsoft_support | Developer direct support, SwiftSoft identity confirmation |
| https://discord.com/invite/jqQyAZdbPz | Discord server |
| https://profile.t.me | Telegram profile links used in social screen |
| https://github.com/cheat-engine/cheat-engine/releases/download/7.4/CheatEngine74.exe | Auto-downloads CheatEngine 7.4 directly from GitHub |
| https://net.geo.opera.com/opera/... | Opera browser offer reactivation endpoint |
| https://www.avast.com/privacy-policy | Avast offer landing page |
| https://www.opera.com/privacy | Opera privacy policy page |

The CheatEngine download URL is notable. The application fetches and presumably installs CheatEngine 7.4 directly from a specific GitHub release URL without a separate user prompt for that specific download. This means the application is pulling and executing a third-party binary from a remote source as part of its normal operation.

## Hardcoded IP Addresses

Three IP addresses were extracted from app.so:

```
127.0.0.1
192.168.2.220
01.01.01.01
```

127.0.0.1 is localhost, expected in any application. 01.01.01.01 is a malformed address and is likely a placeholder or test value. 192.168.2.220 is a private LAN address hardcoded in a production binary. This is a development artifact, the developer's local machine IP that was not removed before the build was published. It is documented in full in 17_attribution_artifacts.md.

## In-Memory Network String Scrubbing

Beyond its own network communications, the application contains regex patterns used to identify and erase network-related strings from the memory of target game processes. These patterns are part of the game memory string scrubbing system documented in 12_string_scrubbing.md.

Patterns extracted from app.so:

```
regex:allow_replace:[a-zA-Z]+:\/\/(\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}|\[[0-9a-fA-F:]+\])
regex:allow_replace:(\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}|\[[0-9a-fA-F:]+\]):\d+
regex:allow_replace:\.exe
```

The first pattern matches protocol-prefixed URLs containing IPv4 or IPv6 addresses. The second matches bare IP:port combinations. The third matches executable file references. These patterns are applied against game process memory to erase strings that could be detected by anti-cheat memory scanners looking for network addresses or executable references in injected code regions.
