# Streamer Launcher - Changelog

## [v3.6.1] - 2026-11-06
### Fixed & Hardened
- **Calendar Navigation & Natural Scrolling**:
  - Resolved focus trap where focus was caught on the action buttons (`Play` / `Trailer`) by implementing dynamic viewport inspection (`getVisibleOrActiveEpisodeCard`), enabling immediate escape back to adjacent visible episode cards via `DPAD_LEFT`.
  - Replaced relative coordinate calculation (`offsetDescendantRectToMyCoords`) with invariant vertical offset computation (`getDescendantTopInScrollView`), eliminating scroll coordinate corruption and viewport pinning.
  - Restored natural card-by-card scrolling: removed forced top-pinning on focus transitions, allowing smooth focus progression down the episode list while strictly constraining viewport scrolling to boundary thresholds.
  - Restored two-way navigation between the top Tab bar (`My shows` / `All premieres`) and episode cards without losing focus.
- **Homescreen Now Playing Pill Persistence**:
  - Fixed missing Now Playing pill below the cricket score on the homescreen by synchronizing exit animation in `closeMusicOverlay`, ensuring `updateNowPlayingPill()` executes after the music overlay visibility transitions to `GONE`.
  - Aligned pill visibility conditions with Quick Settings (`currentTrack != null`), allowing the pill to remain persistent across active, buffering, and paused playback states with dynamic Play/Pause status indication.
  - Added synchronous dock gap adjustment (`syncHomeGapWithTopBar`) on pill visibility updates, ensuring no overlap or layout jump between the clock, cricket score, and now playing widgets.
- **Global Home Button System Reset**:
  - Hardened `HomeOverride` accessibility service with explicit task-clearing intent flags (`FLAG_ACTIVITY_NEW_TASK or FLAG_ACTIVITY_CLEAR_TOP or FLAG_ACTIVITY_SINGLE_TOP`) and Android 14 compliant package-explicit broadcast delivery (`CLOSE_PANELS`).
  - Implemented `resetToHomePage1()` in `MainActivity` to reliably dismiss all open overlays (TV Calendar, Streamer Music, Cricket Modal, Quick Settings, In-App Trailer Player, Settings, All Apps) and smooth-scroll back to Page 1 dock (focusing YouTube) upon pressing the remote HOME button from anywhere in the launcher.
- **Streamer Music UI Exit Transition Polish**:
  - Replaced instantaneous dismissal with a synchronized 120ms alpha fade-out and dock gap recalculation in `closeMusicOverlay()`, eliminating homescreen dock jumping when exiting back to the homescreen.
- **Android 9 / Legacy Storage Permissions**:
  - Added startup permission request handling for legacy `READ_EXTERNAL_STORAGE` and `WRITE_EXTERNAL_STORAGE` on devices running Android 9 / API 28 and below, while preserving modern scoped storage behavior on Android 10+.

---

# Streamer Launcher - Changelog

## [v3.6] - 2026-11-05
### Added & Personalization
- **IMDb Quality Verification Gate for "For You" Recommendations**:
  - Implemented dual-threshold IMDb rating verification gate ensuring every title recommended in personalized "For You: Movies" and "For You: TV Shows" catalogs satisfies strict minimum quality standards before caching and publication.
  - OpenAPI-aligned client retrieval with batch coalescing, deduplication, and resilient fallback handling against network / IMDb provider outages.
- **Global Music Mixes Expansion**:
  - Expanded `GLOBAL_CATALOG` in `RegionalMusicDirectory` to 24 diverse, curated playlists across world genres (90s Alternative/Grunge, 2000s Pop Nostalgia, Tropical & Deep House, Hip-Hop Bangers, Indie Pop, Retrowave/Synthwave, Cinematic Soundtracks, Peaceful Piano, Late Night Jazz, Disco & Funk, Modern Country, High Energy Workout, and more).
  - Clean regional personalization: automatic location lookup using `geo_city` / `geo_country` ensures international locations (e.g. Amsterdam, London, Cairo, Mexico City) strictly receive global/Western mixes without inappropriate Indian genre bleed.
  - Dynamic music search hints and suggestion chips automatically adapting based on user's geographic region.

### Fixed & Hardened
- **Streamer Music UI Navigation Crash**:
  - Resolved fatal `IllegalArgumentException: parameter must be a descendant of this view` triggered by DPAD UP from quick playlist cards or DPAD LEFT from the Rotate Mixes button.
  - Implemented boundary focus trapping, container focus disabling (`android:focusable="false"` on `musicOverlay`), and defensive rescue logic to prevent remote focus from escaping the overlay to background views.
- **Cricket Settings Reordering & Validation Gating**:
  - Reordered Cricket settings submenu under Streamer settings -> Appearance -> Cricket score:
    1. *CricketData API key* (top position)
    2. *Cricket scores* (toggle)
    3. *Refresh scores now*
  - Enforced gating: toggling scores and refreshing remains strictly disabled until the API key is entered and validated (`checkKey()`), automatically enabling upon successful verification.
- **Cricket Toggle Homescreen Dock Fluctuation**:
  - Fixed background homescreen layout jitter and dock displacement when toggling cricket scores ON/OFF by introducing synchronous layout measurement (`syncHomeGapWithTopBar`), eliminating multi-frame asynchronous layout latency.
- **Streamer Files Scroll Position & Viewport Retention**:
  - Fixed viewport jumping in Streamer Files when returning from a subfolder in internal storage.
  - Implemented persistent scroll state and pixel offset tracking (`FolderNavState` with `scrollToPositionWithOffset`), preserving exact scroll offset and visible directory structure upon pressing Back.
- **Changelog Navigation & Download Progress Polish**:
  - Enlarged changelog display container with standalone D-Pad focusability and smooth scrolling.
  - Relocated "Update now" button to screen bottom with generous breathing room and decoupled download progress bar from the update action button.
  - Shifted developer GitHub attribution footer to the screen bottom for clean visual hierarchy.

---

## [v3.5] - 2026-11-05
### Added
- **Streaming SIMKL TV Calendar Parser**:
  - Memory-efficient streaming JSON parser (`StreamingJsonReader`) processing large multi-megabyte calendar feeds with bounded memory allocation.
  - Rolling 16-day windowing (-1 to +14 days) and two-day global timezone margin filter ensuring airing badge synchronization across any local timezone.
  - Instantaneous calendar day-group rendering and cached airing badges with effective weather-location timezone alignment (IST/local).
- **Consolidated Watchlist Request Engine**:
  - Single-flight request coalescing (`fetchWatchlistCoalesced`) for SIMKL watchlist requests, eliminating duplicate concurrent queries.
  - Mathematical isolation of in-flight tasks keyed by account, session generation, media type, and watchlist status (`${account}_${gen}_$catalogId`).
  - Cache availability differentiation (`hasCachedItems`) ensuring valid empty watchlists (`[]`) are persisted on disk and avoid recurring network requests.
  - Dynamic wallpaper catalog cache reuse in `SofadropEngine`, eliminating up to 6 redundant network requests per rotation.
- **Watched-Data Sync Single-Flight Coalescing**:
  - Coalesced concurrent full watched-data synchronization requests (`fetchWatchedData`) under `publicationLock`, preventing simultaneous multi-endpoint network bursts (~2.8 MB each).
  - Bounded executor lifecycle hardening with safe in-flight task removal and reference identity verification.

### Changed
- **Context-Menu Watched Status Optimization**:
  - Completely decoupled SIMKL watched-status inspection from UI-thread JSON disk parsing, moving watched cache loading to asynchronous background warmup.
  - Reduced context-menu opening latency from 60–800 ms to instantaneous (<1 ms / 0 ms main-thread I/O) on resource-constrained TV hardware.
- **Low-Memory Strategy Hardening**:
  - Proactive memory-pressure trimming (`TRIM_MEMORY_RUNNING_CRITICAL`), guarded bitmap recycling, and safe fallbacks for ~929 MB RAM devices.
  - Cold startup Total PSS reduced to 48.4 MB (down by ~4 MB from v3.4), maintaining system operating status `normal`.

### Fixed
- **Cross-Account State Leakage & Disconnect Cleanup**:
  - Fixed `CalendarManager.clearUserShows()` to delete `simkl_user_shows.json` from disk upon account logout or disconnect.
  - Decoupled `clearUserShows()` from ordinary calendar cache invalidation (`clearCache()`), preserving user tracked shows during weather timezone updates.
  - Implemented late asynchronous write protection, rejecting out-of-order in-flight responses if account or session generation changed during fetch.
- **Watchlist Infinite Re-fetch Loop**:
  - Resolved `CatalogManager` defect that treated empty watchlists as network failures, advancing `lastRefresh` timestamp and clearing `lastError`.

---
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
