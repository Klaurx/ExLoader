# 03 - File Tree

Full installed file tree as observed from SFX extraction and string analysis. Annotations describe the role of each component.

```
C:\Program Files\ExLoader\
    ExLoader.exe
        Flutter Windows runner, 204 KB.
        Hosts the Flutter engine and delegates all application logic to data/app.so.
        Launched as the primary UI process after installation.

    vpnshumans.exe
        Byte-for-byte identical to ExLoader.exe (same SHA256).
        Launched as a secondary process under a different name to handle network operations.
        The name implies VPN or network helper role but the binary is the same runner.

    flutter_windows.dll
        Flutter engine, 20 MB.
        Provides the Dart runtime, rendering pipeline, and plugin host.

    audioplayers_windows_plugin.dll
        Flutter plugin for audio playback.
        Used for UI interaction sounds confirmed in resources/audio/.

    permission_handler_windows_plugin.dll
        Flutter plugin for requesting system permissions.

    url_launcher_windows_plugin.dll
        Flutter plugin for opening URLs in the default browser.
        Used in the bundleware offer redirect flow.

    msvcp140.dll
    vcruntime140.dll
    vcruntime140_1.dll
        Microsoft Visual C++ runtime redistributables bundled with the installer.
        Standard dependency for MSVC-compiled Windows applications.

    data/
        app.so
            All Dart application logic compiled to native x86-64 AOT code, 21 MB.
            Primary analytical target. Contains all behavioral logic documented
            across this report.

        icudtl.dat
            ICU unicode data file. Required by the Flutter engine for
            internationalization support. Consistent with 22-language distribution.

        flutter_assets/
            AssetManifest.json
                Maps all bundled asset paths to their file locations.
                Used to reconstruct the full UI feature set. See 16_ui_feature_map.md.

            FontManifest.json
                Declares the NoirPro font family used across the UI.

            NOTICES.Z
                Compressed open-source license notices for bundled libraries.

            resources/
                audio/
                    AbominationPissed_DE.wav
                    AbominationPissed_EN.wav
                    AbominationPissed_RU.wav
                        Localized voice lines in German, English, and Russian.
                        Played as UI feedback sounds.

                    Csgo_hover.wav
                    Csgo_press.wav
                    Fortnite_hover.wav
                    Fortnite_press.wav
                    Standard_hover.wav
                    Standard_press.wav
                    Steam_hover.wav
                    Steam_press.wav
                    Windows_notification.wav
                        Per-game and per-theme UI interaction sound sets.
                        Confirm the application has dedicated UI themes for
                        CS:GO, Fortnite, and Steam.

                backgrounds/
                    Anime.jpg
                    CatsDay.jpg
                    Cyberpunk.jpg
                    Easter.jpg
                    Fallguys_v1.jpg
                    Fallguys_v2.jpg
                    FishingDay.jpg
                    Genshin.jpg
                    God of War.jpg
                    Halloween.jpg
                    Halo.jpg
                    IceCreamDay.jpg
                    JokeDay.jpg
                    LoveDay.jpg
                    NewYear.jpg
                    Ori and the Blind Forest.jpg
                    SchoolDay.jpg
                    SpaceDay.jpg
                    SummerStart.jpg
                    TastyFoodDay.jpg
                    Warcraft.jpg
                        Seasonal and game-themed background images for the UI.
                        The range of titles (CS:GO, Fortnite, Genshin, Warcraft,
                        God of War, Halo, Fall Guys) indicates the breadth of
                        supported or targeted games.

                flags/
                    (22 flag images, one per supported language)
                        Confirms 22-language localization is fully implemented
                        at the asset level, not just in strings.

                fonts/
                    NoirPro-Bold.otf
                    NoirPro-Light.otf
                    NoirPro-Medium.otf
                    NoirPro-Regular.otf
                    NoirPro-SemiBold.otf
                        Commercial font family bundled with the application.
                        Indicates investment in UI presentation quality.

                images/
                    Full UI icon set. Each icon corresponds to an application
                    feature or screen. Full mapping in 16_ui_feature_map.md.

                shaders/
                    background_image.frag
                    modification_card_image.frag
                    raindrops.frag
                    shimmer_effect.frag
                    winter.frag
                        Custom GLSL fragment shaders for animated UI effects.
                        Rain, snow, shimmer, and background rendering.
                        Confirms the application has a purpose-built GPU-accelerated
                        UI, consistent with the commercial product presentation.
```
