# 17 - Attribution Artifacts

## Overview

Several strings extracted from app.so provide attribution evidence pointing to the developer's identity, geographic location, and build environment. These were not intentionally disclosed by the developer.

## Developer Identity Confirmation

Three independent indicators, each sufficient on its own to attribute the software to the same entity:

```
https://t.me/swiftsoft_support
package:exloader/logic/...
.exloader.net/end_user_license_agreement
```

The Telegram support handle swiftsoft_support identifies the developer as operating under the name SwiftSoft. The Dart package namespace package:exloader confirms the application's internal project name matches its public name. The EULA endpoint on exloader.net confirms the developer owns and operates that domain as their primary backend infrastructure.

## Leaked Development Machine IP

A private LAN IP address was found hardcoded in the production binary:

```
192.168.2.220
```

192.168.2.220 is a non-routable private address in the 192.168.x.x range. It has no function in a distributed application. Its presence in the binary indicates it was used during development, likely as a local test server address, and was not removed before the production build was published. This is a build hygiene failure.

This address does not directly identify the developer's physical location or public IP address. It indicates their development machine was assigned 192.168.2.220 on their local network at the time of the build. In combination with other attribution indicators it narrows the build environment.

## Compilation Timestamp and Timezone

```
Sun Apr 19 11:24:28 2026
```

PE compilation timestamps can be manipulated, but in the absence of evidence of tampering this places the build at 11:24 AM on April 19, 2026. The UTC+3 timezone offset inferred from surrounding context is consistent with the developer's apparent geographic base given the Telegram infrastructure, the density of Russian-language strings, and the Yandex browser inclusion in the bundleware offer stack.

## Telegram Infrastructure

```
https://t.me/ExLoader
https://t.me/exloader_chat
https://t.me/swiftsoft_support
```

Three separate Telegram resources are embedded: the public channel, the community chat, and the direct developer support handle. All three use Telegram, which is strongly associated with Eastern European and Russian-speaking developer communities in this context. The support handle directly names the developer entity.

## Discord

```
https://discord.com/invite/jqQyAZdbPz
```

The Discord invite link is hardcoded. Reporting this link to Discord Trust and Safety will allow them to correlate it with the server owner's account.
