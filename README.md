# Streamer Launcher

<p align="center">
  <strong>An ultra-fast, minimal, and elegant home screen engineered specifically for Android TV and Google TV.</strong>
</p>

<p align="center">
  <a href="https://github.com/raviprakashji/Streamer-launcher/releases/latest"><img alt="Version" src="https://img.shields.io/badge/Release-v2.0-blue?style=flat-square"></a>
  <img alt="Android 8.0+" src="https://img.shields.io/badge/Android-8.0%2B-3ddc84?style=flat-square">
  <img alt="APK size" src="https://img.shields.io/badge/APK-3.6%20MB-success?style=flat-square">
  <a href="https://github.com/raviprakashji/Streamer-launcher/actions"><img alt="CI Build" src="https://img.shields.io/github/actions/workflow/status/raviprakashji/Streamer-launcher/build-release.yml?branch=main&style=flat-square"></a>
  <img alt="Ad-Free" src="https://img.shields.io/badge/Ads-None-brightgreen?style=flat-square">
  <a href="LICENSE"><img alt="GPL-3.0 License" src="https://img.shields.io/badge/License-GPL--3.0-orange?style=flat-square"></a>
</p>

---

## Product Showcase

<p align="center">
  <a href="https://github.com/raviprakashji/Streamer-launcher/releases/download/v1.0/streamer-demo.mp4">
    <img src="docs/screenshots/home.png" alt="Streamer Launcher Demo Video Preview" width="85%">
  </a>
</p>

<p align="center">
  <em><strong><a href="https://www.youtube.com/watch?v=ZTTD3AxTKcA">Click here to watch the HD Video Demo (YouTube)</a></strong></em>
</p>

### Core Interface

| Home Screen (Page 1) | Discover Catalogs (Page 2) |
| :---: | :---: |
| <img src="docs/screenshots/home.png" width="100%" alt="Home Dock and Live Weather"> | <img src="docs/screenshots/discover.png" width="100%" alt="Discover Catalogs and Synopsis"> |
| *Streamlined glass dock with adaptable icons, clock, and live weather.* | *TMDB Trending rows, ratings aggregator, and dynamic backdrop sync.* |

| High-Definition Trailer Streaming | Streamer Settings Main Menu |
| :---: | :---: |
| <img src="docs/screenshots/trailer.png" width="100%" alt="In-App Trailer Playback"> | <img src="docs/screenshots/settings.png" width="100%" alt="Streamer Settings Main Menu"> |
| *Built-in ExoPlayer 1440p / 1080p in-app trailer player with scrub bar and HD badge.* | *AMOLED dark frosted settings menu with fast categorized navigation.* |

---

### Settings and Configuration Submenus

| Appearance Submenu | Continue Watching Submenu |
| :---: | :---: |
| <img src="docs/screenshots/settings_appearance.png" width="100%" alt="Appearance Submenu"> | <img src="docs/screenshots/settings_continue_watching.png" width="100%" alt="Continue Watching Submenu"> |
| *Wallpapers (Rockies Day/Dusk, Pure black, TMDB cinematic), photo fade, and rotation timer.* | *Filter per-app sources and restore hidden shows with package icon badges.* |

<p align="center">
  <strong>Discover Catalogs Submenu</strong>
  <br>
  <img src="docs/screenshots/settings_discovery.png" width="70%" alt="Discover Catalogs Submenu">
  <br>
  <em>Toggle discovery catalogs, connect MDBList and SIMKL watchlists, and configure preferred media players.</em>
</p>

---

## Key Features

- **TV Calendar and Premiere Schedule**:
  - Full TV Calendar modal accessible directly from the top navigation bar with My Shows and All Premieres tabs.
  - Fluid continuous scrolling when holding down DPAD navigation across date headers and episode groups.
  - Genuine Premieres filtering: TMDb season debut verification filters out foreign re-runs, repeats, and catalog noise.
  - Instant 0ms view loading with persistent disk caching and downsampled RGB_565 poster decoding.
- **Backup and Restore System**:
  - Complete configuration export and import under Streamer Settings -> Backup / Reset.
  - Backs up and restores dock ordering, wallpaper preferences, screensaver modes, weather locations and units, live CricketData API key, Homescreen Dimming timeouts, and SIMKL credentials.
  - Seamlessly re-applies preferences across device re-installations while matching currently installed TV applications.
- **Ambient Homescreen Dimming**:
  - Customizable idle dimmer under Appearance settings (choices: 5s, 10s, 15s, 30s, 1m, 2m, 5m, 15m, or Off).
  - Smoothly fades the dock, clock, weather, and top navigation bar to transparent during inactivity, transforming the TV into a clean art canvas.
  - Preserves live Cricket match pill visibility during dimming.
  - Instantly wakes up upon any remote D-pad input or touch interaction.
- **Zero-Permission AOT Compilation**:
  - Bundles Android Jetpack Baseline Profiles (androidx.profileinstaller) to compile DEX bytecode to native machine instructions directly upon installation and update.
  - Requires zero Developer Options, no USB Debugging, and no Root privileges.
- **Ultra-Lean and Memory Optimized (3.6 MB APK)**:
  - Sub-4MB APK footprint with instant cold boot.
  - Deep memory trim: 89% reduction in active views, saving over 100 MB of native memory and 50% of GPU buffer memory.
  - Deadlock-free background executor architecture for smooth, stall-free operation on low-RAM TV hardware.
- **Live Cricket Scores Pill**:
  - Live cricket match pill next to weather on the home screen powered by CricketData API.
  - Interactive popup scorecards with compact card layout, vertical glass scrollbar, and seamless D-pad navigation.
- **Media Player Relay and Voice Search Disambiguation**:
  - Overlays a direct "Watch in [Player]" button on Google Assistant search result pages and TV detail screens.
  - Intelligent media title resolution ranking that correctly prioritizes exact title and year matches.
- **TMDB Discover Catalogs**:
  - Browse trending movies, popular series, top rated titles, and curated genres.
  - Dynamic synopsis cards with multi-platform ratings (IMDb, TMDb, Rotten Tomatoes, Trakt, Metacritic).
- **MDBList and SIMKL Watchlists**:
  - Connect personal or public MDBList and SIMKL watchlists.
  - Automatically fetches and updates watchlist rows directly onto the Discover page.
- **Built-In HD Trailer Streaming**:
  - Seamless in-app ExoPlayer trailer playback supporting up to 1440p / 1080p.
  - Video resolution badge, scrubbable progress bar, and instant play/pause without leaving the launcher.
- **Preferred Media Player Launcher**:
  - One-click launch to your favorite external video player: Nuvio, Nova Player, Stremio, Just Player, Kodi, VLC, MX Player, or prompt on every play.
- **Continue Watching Aggregator**:
  - Resumes ongoing media from Android TV Watch Next and supported media applications.
  - Hidden show management with app icon badges and automatic pruning of uninstalled apps.

---

## Technical Architecture

```
+-------------------------------------------------------------------------------+
|                               Streamer Launcher                               |
+---------------------------------------+---------------------------------------+
|              Data Layer               |               UI Layer                |
|  - TMDB API Client                    |  - Lightweight Android View Hierarchy |
|  - MDBList / SIMKL Engine             |  - SnapScrollView (60fps D-pad Paging)|
|  - TV Calendar & Premieres Filter     |  - DockPillView (Frosted Blur Glass)  |
|  - Backup & Restore JSON Engine       |  - Ambient Dimmer Alpha Controller    |
|  - Android WatchNext Provider         |  - Dynamic Backdrop Renderer          |
|  - In-App YouTube Extractor           |  - Glass Scorecard Overlay Container  |
|  - CricketData Live Score Engine      |  - Media Player Relay Accessibility   |
|  - GitHub Auto-Update Service         |  - TV Calendar Modal & Timeline View  |
+---------------------------------------+---------------------------------------+
|                          Hardware & Media Pipeline                            |
|  - Media3 ExoPlayer 1.5.1 (1440p/1080p Video + AAC Audio Stream Mux)          |
|  - Android Jetpack ProfileInstaller (Zero-Permission AOT Machine Compilation) |
|  - RGB_565 Decoded Bitmaps (Zero GC Stalls, Native Heap Savings)              |
|  - Instant Cache Flushing on Backgrounding (Near-Zero Idle Memory Footprint)  |
|  - R8 Full-Mode Minification & Multi-Pass Optimization (3.6 MB Total APK)    |
+-------------------------------------------------------------------------------+
```

---

## TV Remote Navigation Guide

| Action | Remote Key / Gesture |
| :--- | :--- |
| **Move Focus** | <kbd>Up</kbd> <kbd>Down</kbd> <kbd>Left</kbd> <kbd>Right</kbd> Directional Pad (D-pad) |
| **Launch App / Media** | <kbd>OK</kbd> / <kbd>Enter</kbd> (Center Button) |
| **Switch Pages** | <kbd>Down</kbd> from dock transitions smoothly to Page 2 (Discover Catalogs) |
| **All Apps Shortcut** | Press <kbd>Up</kbd> from anywhere in the app dock to focus the All Apps icon |
| **Open TV Calendar** | Select the Calendar icon from the top bar |
| **Quick Play / Trailer** | Highlight movie poster -> Press <kbd>Up</kbd> to access Play and Trailer buttons |
| **App Management** | Long-press <kbd>OK</kbd> on any dock app to rearrange or hide |
| **Back / Exit Modal** | <kbd>Back</kbd> key closes trailers, search dialogs, calendar, and settings |

---

## Installation

### Option 1: Direct APK Download
Download the latest pre-compiled release from the [Releases Page](https://github.com/raviprakashji/Streamer-launcher/releases/latest):
- **Streamer.launcher_v2.0.apk** (3.6 MB)

### Option 2: Install via ADB
```bash
# 1. Connect to your TV over Wi-Fi
adb connect <your-tv-ip>:5555

# 2. Install the APK
adb install -r "Streamer.launcher_v2.0.apk"
```

---

## Building from Source

### Prerequisites
- **JDK 17** (Temurin or OpenJDK recommended)
- **Android SDK** (API Level 36, Build Tools 34.0.0)

### Build Commands
```bash
# Clone the repository
git clone https://github.com/raviprakashji/Streamer-launcher.git
cd Streamer-launcher

# Run unit tests
./gradlew test

# Compile release APK
./gradlew assembleRelease
```
The compiled APK will be located at:
`app/build/outputs/apk/release/Streamer.launcher_v2.0.apk`

---

## License

This project is licensed under the **GNU General Public License v3.0 (GPL-3.0)**. See the [LICENSE](LICENSE) file for complete details.
