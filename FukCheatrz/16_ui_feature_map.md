# 16 - UI Feature Map

## Overview

The full UI feature set was reconstructed from AssetManifest.json, which maps every asset file bundled with the application to its path. Icon filenames are descriptive and directly indicate the feature or screen they represent.

## Feature Map

| Asset filename | Inferred feature or screen |
|---|---|
| injection.png | Injection method selector. User chooses between available injection modes. |
| kernel.png | Kernel driver mode toggle. Enables the driver loading behavior documented in 07_kernel_and_driver.md. |
| eac.png | EasyAntiCheat interaction screen. EAC is the anti-cheat system used by Fortnite and other titles. |
| bios.png | BIOS-level hardware identifier spoofer screen. |
| flashdrive.png | Removable storage identifier spoofer screen. |
| harddrive.png | Fixed storage identifier spoofer screen. |
| defender.png | Windows Defender interaction screen. Likely presents exclusion management. |
| profanity.png | Game memory string scrubber configuration. See 12_string_scrubbing.md. |
| trash.png | Trace cleanup UI. Triggers the anti-forensic routines documented in 09_anti_forensics.md. |
| log.png | Log viewer or log cleanup screen. |
| crash.png | Crash reporting screen. |
| complain.png | User complaint and report submission screen. |
| memory.png | Memory manipulation configuration screen. |
| engine.png | Game engine configuration screen. |
| fps.png | Frames-per-second related cheat feature toggle. |
| hitbox.png | Hitbox-related cheat feature toggle. |
| mods.png | Mod browser and loader screen. |
| updates.png | Application auto-update screen. |
| debug.png | Debug interface, likely developer-facing. |
| offline.png | Offline mode screen. Allows the application to operate without a backend connection. |
| weather.png | Geo or IP display screen. Likely shows the user's detected country or IP, used in the bundleware geo-targeting flow. |
| privacy.png | Privacy settings or privacy policy display screen. |
| socials.png | Social links screen. Displays the Telegram and Discord community links. |

## Game-Specific Themes

Audio assets confirm dedicated UI sound themes for specific games:

```
Csgo_hover.wav
Csgo_press.wav
Fortnite_hover.wav
Fortnite_press.wav
Steam_hover.wav
Steam_press.wav
Standard_hover.wav
Standard_press.wav
```

Background images confirm game-specific visual themes. Confirmed titles with dedicated backgrounds:

```
CS:GO (implied via Csgo audio)
Fortnite (implied via Fortnite audio)
Genshin Impact
God of War
Halo
Warcraft
Fall Guys
Cyberpunk 2077
Ori and the Blind Forest
```

The breadth of supported titles, spanning competitive shooters, battle royales, open-world RPGs, and action games, indicates ExLoader is not targeted at a single game but is a general-purpose loader designed to work across a wide range of titles.
