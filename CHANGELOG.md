# Streamer Launcher - Changelog

## [v3.4] - 2026-11-04
### Added
- **Streamer Music Integration**:
  - Full-featured YouTube Music audio streaming engine using Media3 ExoPlayer with background playback and dynamic waveform visualization.
  - Regional mixes and playlists discovery directory on Page 1, instant music search with debounced queries and result suggestions.
  - Status bar Now Playing pill widget with smooth marquee track title, album artwork, and direct shortcut back to playback view.
  - Interactive playback controls: seekbar scrub with remote DPAD (forward / backward by 5 seconds), shuffle toggle, and track queue management.
  - Disconnect detection with warning prompt to reconnect internet during streaming interruption.
- **Floating Quick Settings Panel**:
  - Compact glass floating panel positioned directly beneath the Quick Settings top-bar action with full D-Pad focus trapping.
  - Live Now Playing tile within Quick Settings showing real-time playback progress bar, duration timestamps, and one-click navigation to the music player.
  - Exact D-Pad cyclic navigation matrix with continuous snake wrapping across tiles whether Now Playing is active or hidden.
- **Restructured Homescreen Dimming Preferences**:
  - Subpage under Streamer settings -> Appearance -> Dim homescreen components.
  - Independent toggles for Dim Cricket score and Dim Now playing.
  - Fixed-anchor layout preservation: dimming the cricket score pill preserves its layout space so the Now Playing pill remains strictly stationary without jumping.
  - Expanded dim timeout durations: added 45 sec (options from Off to 15 min).

### Changed
- **Unified Quick Settings Tile Aesthetics**: Cleaned up tile color schemes on LocalSend and Input to match the sleek neutral TV glass design.
- **Navigation Flow Polish**: Streamer Settings back navigation seamlessly restores focus to Quick Settings button on homescreen.

### Fixed
- **Home Button Exit Dock Shift**: Eliminated visual app dock glitch when exiting music playback directly to the homescreen via the Home button.
- **Music UI Banner Highlighting**: Restored high-contrast focused style and focus breathing padding for the Now Playing banner when navigating from the Rotate Mixes button.
- **Search Card Geometry**: Fixed card positioning in search results so focused cards maintain stable alignment without shifting adjacent cards.

---

## [v3.3] - 2026-11-04
### Added
- **MDBList Private & Dynamic Lists Integration**: Automatic import of user lists (dynamic, linked, and static) from connected MDBList accounts with on-device API resolution and deduplication.
- **Dynamic Order Preservation**: Disabled catalogs are cleanly omitted from Reorder catalogs; re-enabling appends them to the bottom to protect customized catalog arrangements.

### Changed
- **Page 1 Collapsible Navigation**: Updated top bar layout order (`All apps`, `Calendar`, `System Settings`, `LocalSend`, `Streamer Files`, `WiFi`, `Input`, `Refresh`, `Streamer Settings`, `More`). `All apps`, `Calendar`, and `System Settings` remain permanently visible, and DPAD navigation smoothly follows visual icon sequence.
- **Input Action Modernization**: Renamed homescreen HDMI button and header to "Input".
- **Discover Catalogs Menu Restructuring**: Restructured `Discover catalogs` subpage; moved "Play from assistant UI" exclusively into `Preferred media player`, and relocated "Watched content filtering" and "Taste profile" under `SIMKL`.

### Fixed
- **Discover Catalogs Boundary Stability**: Resolved edge overscroll glitch when navigating past the last catalog in `catalogsRailScroll` by disabling nested scrolling, enforcing a 320dp minimum container height, and guarding `HomeScrollView` focus deltas.

---

## [v3.2] - 2026-11-03
### Added
- **Direct Hardware Screenshot Capture Engine**: Bypasses SystemUI via `AccessibilityService.takeScreenshot(Display.DEFAULT_DISPLAY, ...)` (API 30+), captures `HardwareBuffer` directly from SurfaceFlinger, converts to Bitmap, and writes to `Pictures/Screenshots` via `MediaStore` with confirmation Toast.
- **Accessibility Security Policy Compliance**: Declared `canTakeScreenshot="true"` and `canPerformGestures="true"` in `home_override.xml`.

### Changed
- **Button Mapper System Actions Cleaned Up**: Removed non-TV compatible options (`Recent apps`, `Notifications`, `Quick settings`). Only TV-applicable actions (Home, Back, Power menu, Take screenshot, Sleep display) remain.
- **Navigation Intent Fallbacks**: Added direct `Intent(ACTION_MAIN).addCategory(CATEGORY_HOME)` and `onBackPressed()` fallbacks when accessibility key injection is restricted.

---

## [v3.1] - 2026-11-02
### Added
- **AI SIMKL Taste Profile Engine**: On-device personalized recommendations ("For You: Movies" and "For You: TV Shows").
- **Real-time SIMKL Watchlist Sync**: In-place toggle for watchlist state with zero UI disruption.
- **Package Uninstallation & Dock Auto-Sync**: Instant removal and auto-insertion of apps in dock.
- **Automated Cricket Fixture Format Discovery**: Deep API resolution distinguishing ODIs, Tests, and T20s.

---

## [v3.0] - 2026-10-31
### Added
- Integrated high-speed LocalSend wireless file transfer.
- Built-in Leanback "Streamer Files" file manager and APK installer.
- Hardware HDMI/AV source switching.
- Accessibility Button Mapper for remote controls.
