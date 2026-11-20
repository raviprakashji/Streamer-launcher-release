## [v4.2.2] - 2026-11-20
### Added, Fixed & Enhanced (vs v4.2.1)
- **Universal Floating Overlays Over Running Apps**:
  - **Overlay Architecture**: Remapped remote buttons now launch floating overlays directly over any foreground application or active video playback (e.g., YouTube, Netflix, Prime Video) without switching tasks or displaying the home screen.
  - **Quick Settings Overlay**: Slide-in floating panel for Wi-Fi management, audio output switching, ambient settings, sleep timers, and media controls over any active app.
  - **Cricket Scores Overlay**: Floating top-right live match score card with marquee scrolling, full over/wicket details, and auto-dismissal.
  - **LocalSend Overlay**: Complete dual-tab LocalSend transfer interface floating over apps with full file-picker and app-sharing support. Fixed an issue where picking files or pressing Back in file manager closed background apps by isolating task affinities.
  - **Music HUD Overlay**: Floating bottom playback bar with synchronized lyrics and media transport controls.
  - **Streamer Settings Overlay**: Side panel launcher settings overlay floating directly over foreground apps with dark scrim and smooth back navigation.
  - **App Dock Overlay**: Floating quick dock with smooth exit animations and clean D-pad traversal.
  - **Background Catalog & Score Refresh**: Asynchronous catalog and live score refresh triggered via remote buttons with toast feedback, without leaving the active app.
- **Button Mapper Performance & Architectural Optimizations**:
  - **$O(1)$ Primitive Key Lookup**: Replaced linear key scans with primitive-indexed `SparseArray` lookups (`keyCodeMap` and `scanCodeMap`), achieving zero-allocation, sub-microsecond key dispatch on TV remotes.
  - **Dynamic Accessibility Key Filtering Bypass**: Strips `FLAG_REQUEST_FILTER_KEY_EVENTS` when idle (no buttons mapped, no overlays visible), eliminating accessibility IPC input latency across the entire Android TV OS.
  - **Asynchronous Disk & Graphic Execution**: Moved hardware buffer bitmap wrapping and screenshot disk writing off the main UI thread into dedicated `AppExecutors.diskIo` thread pools.
  - **Blink-Free Compositor Transitions**: Fixed hardware layer flickering over playing video by animating inner card containers directly and consuming `ACTION_DOWN` of Back/Escape while strictly dismissing on `ACTION_UP`.
- **Button Mapper Feature Categorization**:
  - Streamer features are now organized into two distinct, intuitive sections with non-focusable headers:
    - **Works anywhere**: *App dock, Cricket scores, LocalSend, Quick settings, Refresh catalogs, Streamer settings*
    - **Works from homescreen**: *Calendar, Streamer Files, Streamer Music, Streamer search, Switch HDMI input*

---

## [v4.2.1] - 2026-11-20
### Added, Fixed & Enhanced (vs v4.2)
- **Shortcuts & Activities & Activity Explorer Polish**:
  - **Quick Presets Focus Retention**: Fixed DPAD UP navigation from preset cards in "System & Dev" and "Files & Hardware" categories resetting to "All Presets". Focus now accurately returns to the active category filter pill.
  - **Zero-Flicker Tab Navigation**: Removed 150ms artificial delay and implemented view retention across Activity Manager tabs, eliminating jitter, re-inflation lag, and focus-stomping.
  - **Quick Presets IPC Caching**: Caches resolved system settings intents to eliminate 12 synchronous `PackageManager` IPC calls on every render and category switch.
  - **Declared Activities LRU Cache**: Implemented a 32-entry LRU cache for package activities in Activity Explorer, making app-switching instantaneous with zero Binder overhead.
  - **Activity Launcher Drawer Tile Decode Caching**: Tile bitmap decode is now cached in `iconCache`, eliminating repeated flash storage reads on All-Apps drawer redraws.
  - **Activity Explorer DPAD Traversal Optimization**: Removed redundant recursive `findViewById` loop that ran across all activity cards on every single package DPAD step.
  - **Thread-Safe Dexopt Compilation**: Migrated compiler cache to `ConcurrentHashMap<String, String>` and eliminated intermediate collection allocations during installed apps resolution.
- **System-Wide Memory & Performance Engineering**:
  - **Installed Apps $O(1)$ Leanback Resolution**: Replaced linear list scanning (`leanbackActivities.any { ... }`) with $O(1)$ set lookup (`a.packageName in leanbackPkgSet`), speeding up app discovery on boot and resume.
  - **App Folder Caching & Collage Icon LRU**: Added in-memory folder state caching (`cachedFolders`) and 32-entry LRU cache for 2x2 folder collage drawables (`collageCache`), eliminating continuous JSON parsing and bitmap/canvas allocations during home dock navigation.
  - **Recommendation Scoring Regex & Genre Parsing Optimization**: Promoted IMDb ID pattern matching to static compiled regex (`IMDB_ID_REGEX`), extracted static unscripted genre keys, and eliminated duplicate string splitting across candidate scoring passes.
  - **Zero-Allocation Clock Overlay Formatting**: Replaced per-minute `SimpleDateFormat` allocations with thread-safe reusable date formatters.
  - **Buffered Backup & Restore Extraction**: Accelerated wallpaper and custom icon archive restoration by wrapping output streams with `BufferedOutputStream`.
  - **Local ADB RSA Keypair Caching**: In-memory caching for ADB keypairs eliminates redundant disk reads and cryptographic key rebuilding across shell operations.
- **Atomic File Persistence Durability & Recovery Hardening (`SafeFilePersistence`)**:
  - **Strict File-Sync Failure Handling**: Enforced strict `fos.fd.sync()` checks during persistence operations; aborts replacement, safely cleans up incomplete temporary files, and leaves original valid targets completely intact if sync fails.
  - **Deterministic Backup & Recovery Paths**: Added comprehensive testing and validation covering replacement failures after backup creation, crash recovery during interruptions, and stale backup protection.
- **Resource Footprint Reductions**:
  - **Idle / Cold Start Total PSS**: Reduced from ~48 MB to **~40 MB** (16.7% reduction).
  - **Peak Workload Total PSS**: Reduced from ~108 MB to **~83 MB** (23.1% reduction).

---

## [v4.2] - 2026-11-19
### Added, Fixed & Enhanced (vs v4.1.2)
- **Page 2 Snake Navigation**:
  - **Seamless Continuous Grid Navigation**: Pressing `DPAD RIGHT` from the final poster of any catalog row now wraps seamlessly to the first poster of the next row below, enabling natural continuous browsing without backtracking.
  - **Fluid Sidebar Escape**: Pressing `DPAD LEFT` from the first poster of any row returns focus directly to the genre sidebar navigation rail without trap or jarring focus skips.
- **Invisible Dock Theme & Custom Tile Borders**:
  - **Invisible Dock Preset**: Added `Invisible` preset to Dock & Icon Theme settings, completely hiding the dock pill background while maintaining app icon alignment and crisp visibility over wallpapers.
  - **Configurable Tile Borders**: Introduced dedicated border styling (App colour, Glass outline, Off) and custom border opacity sliders specifically tuned for the Invisible dock.
  - **Context-Aware Settings Safeguards**: Focus Effect menu is intelligently disabled and dimmed when the Invisible preset is active to avoid visual conflicts with borderless icons.
- **Aurora Flow Live Wallpaper & Ambient Screensaver**:
  - **Native OpenGL ES Shader Engine**: Integrated `AuroraLiveView` utilizing an optimized GLSL fragment shader simulating natural multi-layered aurora borealis wave physics and color shifting.
  - **Wallpaper & Screensaver Dual Mode**: Selectable directly as a live home screen wallpaper or as an ambient screensaver with zero stutter and low thermal overhead.
- **Animated Icon Idle Optimization**:
  - **Low-Power Idle Freeze**: Animated dock icons now cleanly render their static baseline frame (`Frame 0`) when idle and only engage dynamic frame-by-frame animation upon receiving DPAD focus.
  - **Reduced GPU/CPU Footprint**: Eliminates continuous background surface redraws on Android TV devices, keeping system temps low and preserving memory bandwidth for video playback.
- **Persistent Clock OLED Burn-In Shifting & Large Sizes**:
  - **8-Point Micro-Orbital Burn-in Protection**: Implemented an automated 8-point pixel-shifting cycle every 2 minutes for the persistent clock overlay to prevent OLED burn-in during long home screen sessions.
  - **Large Font Scaling & Offset Adjustments**: Added support for 60f and 72f clock font sizes with proportional vertical margin compensations for optimal readability on 65"+ TV panels.
- **Video Wallpaper Sleep & Standby Lifecycle Fix**:
  - **Zero Standby Freeze**: Fixed an issue where video wallpapers could freeze or display black frames after waking from TV deep sleep standby or after returning from external streaming apps by stabilizing the ExoPlayer surface rebind lifecycle.

---

## [v4.1.2] - 2026-11-18
### Added, Fixed & Enhanced (vs v4.1.1)
- **Bluetooth Connected Devices & Pairing System**:
  - **In-Place Pairing & Dynamic List Sync**: Resolved issue where newly paired Bluetooth devices remained stuck at "Pairing" or failed to appear in "Connected Accessories" without closing and reopening the modal. Decoupled radio discovery cancellation from listener cleanup, keeping bond listeners active throughout pairing.
  - **Dual-Trigger Event Routing**: Both `BOND_BONDED` and `ACTION_ACL_CONNECTED` automatically transition the UI from scan view to normal view, immediately displaying the newly paired device under "Connected Accessories" with audio waveform and battery status.
  - **Flicker-Free In-Place Updates**: Replaced brute-force `removeAllViews()` re-inflation with intelligent snapshot diffing. Existing views update their properties in-place without focus loss, highlight restarts, or waveform animation interruptions, completely eliminating modal blinking.
  - **DPAD HOME Dismissal**: Pressing DPAD HOME while the Bluetooth modal is open cleanly dismisses the modal and returns focus to the first app icon in the app dock.
- **HomeScreen & App Dock Layout Alignment**:
  - **Minimalist Continue Watching Card Redesign**: Reduced minimalist card width by 25% to `212dp` for improved card density and clean Leanback symmetry.
  - **Restored Dock Baseline & Card Spacing**: Restored original app dock bottom baseline positioning and standard visual spacing (`26dp`) between Continue Watching cards and dock top.
- **Volume Key Hold & Home Override Polish**:
  - **Volume Key Hold Repeat Loop**: Implemented rapid volume repeat loop in `HomeOverride` accessibility service for smooth, responsive volume control on remotes without native repeat.
  - **Dynamic Key Filtering Bypass**: Automated volume key filtering bypass while preserving Home key filtering to eliminate stock launcher flash.
- **Backup & Restore System**:
  - **Modular Backup & Restore**: Added granular restore options (Settings, Dock, Layout, Wallpapers, Icon Packs) with dedicated subpages.
  - **Background Looper Stability**: Fixed restore crashes by executing database and preference operations on appropriate background threads with safe UI synchronization.
- **Media Discovery & Wallpaper Polish**:
  - **Catalog Infinite Loop & Focus Jump Fixes**: Resolved pagination loops and focus jumps during catalog browsing, dual-indexing localized synopsis translations.
  - **Expanded Sofadrop Wallpapers**: Increased Sofadrop wallpaper pool to 30 high-resolution interleaved backgrounds.

---

## [v4.1.1] - 2026-11-18
### Added, Fixed & Enhanced (vs v4.1)
- **Backup & Restore Crash Prevention & Integrity**:
  - Automatically triggers the full launcher reset and icon cleanup routine prior to applying any backup restore, completely eliminating startup crashes and icon data corruption when restoring multiple backups back-to-back (e.g. switching between custom icons, Projectivy icons, and default icons).
  - Preserves user's selected "Now Watching" row style (Minimalistic vs Classic) across backup export and restore cycles.
- **Continue Watching Row Horizontal Navigation Freeze Fix**:
  - Resolved Continue Watching row freezing and becoming unresponsive after multiple rapid horizontal focus taps across home screen dock apps by fixing dock app family matching, view cache retention, and async update debounce handling.
- **Photo Fade & Ambient Vignette Polish**:
  - Added dynamic top and bottom vignette overlays (`bg_photo_fade_top` and `bg_photo_fade_bottom`) when Photo Fade is enabled, softening bright wallpapers behind the clock, weather, cricket score, now playing pills, and app dock for optimal eye comfort.
  - Ensured Photo Fade vignette overlays apply exclusively to user wallpapers and do not darken TMDb movie/series backdrop previews on Page 2.
  - **Dock Pill Vignette & Dark Glass Backing**: Embedded bottom vignette gradient sampling into `DockPillView` and applied a subtle translucent dark glass backing (`0x55000000`) beneath specular highlights for *Still Glass*, *Dynamic Glass*, and *Streamer Default* themes on bright wallpapers and when Photo Fade is active, eliminating white glare while retaining specular glossy highlights.
  - Fixed instant crash when opening `Streamer settings → Appearance`.
- **Media Player & Cinematic Details Polish**:
  - **External Player Seamless Resume**: When clicking "Play" on a movie details page and pressing `BACK` from external players (CloudStream, Stremio, Nuvio, Wuplay), launcher resumes directly on that movie's details page with the Play button in focus, gracefully intercepting trailing BACK events and system panel close broadcasts.
  - **Poster Focus Memory**: When dismissing the movie details modal with DPAD BACK, focus returns directly to that movie's poster in the grid with active focus glow and sidebar synopsis, eliminating focus jump to the Search bar.
  - **CloudStream Clean Query**: Passes clean movie/series title without release year to CloudStream URI (`cloudstreamsearch://<Title>`) so provider scraper plugins (StreamPlay, HDHub4U, etc.) immediately return matching posters instead of blank screens.
  - **Preferred Player Menu**: Updated guidance across all languages (`en`, `ar`, `fr`, `tr`) to "Install Nuvio, Wuplay, Stremio or Cloudstream to select a default media player".
- **Page 2 Header Navigation**:
  - Fixed DPAD DOWN from Page 2 header buttons (Search, Calendar, Watched toggle, Category chips, Player pill) directly navigating to the posters grid.

---

## [v4.1] - 2026-11-17
### Added, Fixed & Enhanced (vs v4.0)
- **Unified Media Discovery & Search Explorer (Page 2)**:
  - Transformed Page 2 into a full-screen Media Discovery Explorer and Search Engine (`isSearchPage2Enabled`) with smooth vertical scroll transition between Page 1 and Page 2.
  - Redesigned Page 2 header bar integrating:
    - **Search bar**: Interactive expandable search with Voice Search (`android.speech.RecognizerIntent`) and on-screen keyboard filtering.
    - **TV Show Calendar**: Calendar button positioned directly in the Page 2 header; opens the full TV schedule overlay and returns directly to Page 2 on Back or D-pad Home. Removed separate Calendar icon from the Page 1 top bar.
    - **Watched Visibility Toggle**: Quick toggle (`Watched: Shown` / `Watched: Hidden`) with instant visual feedback and live catalog filtering.
    - **Category Filters**: Filter chips for **All**, **Movies**, and **Shows** with automatic sorting for anime formats.
    - **Catalog Taxonomy & Live Counter**: Inline genre indicator and dynamic title count badge.
    - **Preferred Player Selection Pill**: Quick player switcher (Nuvio variants, Stremio, CloudStream, or None with graceful feedback) right from the header.
  - Three-tier sliding taxonomy explorer: Browse genres (Action, Adventure, Comedy, Crime, Sci-Fi, etc.), subgenres / decades, and rich 5-column poster grids with 0% overlap.
  - Live synopsis box and backdrop previews updating dynamically as posters gain focus.
  - High-performance caching & debouncing: 24-hour disk cache and 800ms search throttling with query LRU cache, completely eliminating cold-start blank screens and navigation lag.
  - Catalog consolidation: Unified MDBList, SIMKL watchlist, and For You recommendation rows under "Discover catalogs Page 2".
- **Custom App Icons & Wallpaper Enhancements**:
  - **Custom App Icon Upload**: Upload custom image icons for any app on the dock or All Apps drawer with full support for PNG, JPG, WEBP, and GIF formats (max file size: **6 MB**).
  - **Video Wallpapers**: Native background video loop playback (MP4, MKV, WebM) with hardware-safe RAM thresholds to prevent out-of-memory issues on low-spec Android TV boxes:
    - Devices with **≤ 1.6 GB RAM**: Max video size **35 MB** (capped at 1080p).
    - Devices with **≤ 2.5 GB RAM**: Max video size **55 MB** (capped at 1080p).
    - Devices with **≤ 3.5 GB RAM**: Max video size **75 MB** (supports up to 4K on capable hardware).
    - Devices with **> 3.5 GB RAM**: Max video size **100 MB** (supports up to 4K).
  - **Animated GIF Wallpapers**: Native infinite-loop playback for GIF and animated WEBP wallpapers using Android `AnimatedImageDrawable`.
- **App Dock & Context Menu Polish**:
  - Fixed dock glass backdrop sampling: Anchored `DockPillView` vertical slice coordinates to resting Page 1 positions (`restingWinY = winLocation[1] + scrollY`), completely eliminating color streaks, distorted wallpaper sampling, and flashing on D-pad Up navigation from Page 2.
  - Renamed main long-press action from "Upload app icon" to **Set app icon ›**, opening submenus for "Upload app icon" and "Choose from icon pack".
  - Conditional submenu chevrons: "Add to folder" now displays the `›` chevron only when at least one folder already exists in that scope (dock or All Apps).
  - Instant menu dismissal: D-pad Home cleanly dismisses hold menus without stuck dialogs or focus leaks.
- **Enhanced USB OTG File Manager**:
  - Full read, write, rename, delete, copy, cut, paste, and backup/restore support on external USB OTG storage (exFAT & FAT32) on Android 11+ via `MANAGE_EXTERNAL_STORAGE` and MediaStore fallbacks.
  - Tree View accordion mode for nested directories with folder item count badges and subfolder creation.
- **CloudStream Player Integration**:
  - Added CloudStream (`com.lagradost.cloudstream3`) as a selectable preferred media player alongside Nuvio and Stremio.
- **1-Click Local ADB Default Launcher Setup**:
  - Built-in ADB default launcher setup wizard with remote focus trapping and equal-height choice boxes.

---

## [v4.0] - 2026-11-13
### Added, Fixed & Enhanced (vs v3.9.2.2)
- **Streamer Search Engine & Full-Screen Modal**:
  - Integrated high-speed unified search across Movies, TV Series, and Anime directly from the home screen top bar (positioned between Wi-Fi and Settings).
  - Built-in Voice Search (`android.speech.RecognizerIntent`) and on-screen D-pad keyboard input support with instant live filtering.
  - Smart Content Categorization: Filter chips for **All**, **Movies**, and **Series**; Anime titles are automatically sorted into Movies or Series based on their media format.
  - Rich Search Result Metadata: Card layout with high-resolution poster artwork, title, release year, unified green type pills (**MOVIE** / **SERIES**), and IMDb & TMDb rating badges.
  - Full non-truncated synopsis text display on the search details view without affecting home/catalog cards.
  - Card soft-focus memory: Retains soft-focus highlight on the active search result card when navigating into action buttons.
  - Isolated media player picker: Lets users choose playback apps (Nuvio variants, Stremio, external players) per search result without modifying global preferences.
- **Remote Button Mapper & Feature Switch Integration**:
  - Added dedicated **Streamer search** toggle row under `Streamer settings → Turn features on/off`, controlling top-bar search button visibility and active search overlay state.
  - Added **Streamer search** action under `Streamer features` in the Button Mapper, allowing quick launch from any remote button or custom keymap.
- **Accurate Media Playback & Deep-Link Resolution**:
  - Strict external ID checks in `resolveImdbSync()` prevent titles without IMDb IDs from falling back to loose text searches that hijack unrelated content (e.g. *Gentlemen (2023)* vs *The Gentlemen (2024)*).
  - Strict year matching (`itemYear == resYear`) and exact title normalization for deep-link targets.
  - Graceful user feedback toast (`No IMDb ID available for "<title>" in <player>`) when an item cannot be resolved in Nuvio or external players.
- **Continue Watching Android 9/10 Compatibility**:
  - Restored `TvContract` Continue Watching on Android 9 and 10 devices by removing restrictive `TV_LISTINGS` permission gates.

---

## [v3.9.2.2] - 2026-11-13
### Added, Fixed & Enhanced (vs v3.9.2.1)
- **Universal Seamless Dock (`glassio` architecture)**:
  - Aligned `DockPillView` with `glassio` single unified surface rendering with style-matched fills (94% black `#EF000000` for Black AMOLED, 6% white liquid wash for Clear, 14% black tint for Frosted Glass, sharp backdrop for Dynamic/Still Glass).
  - Removed artificial decorative linear gradients, specular sheen, and 1px stroke cuts that previously produced horizontal cut lines across the dock.
  - Eliminated hardware RenderNode elevation shadows (`elevation = 0f`) across dock tiles, removing rectangular outline boxes.
- **Wallpaper Slideshow Dock Sync**:
  - Synchronous plate cache invalidation and redraw when slideshow wallpapers rotate, completely eliminating previous wallpapers getting stuck inside dock icons/banners.
- **App Drawer Grid & Navigation Polish**:
  - Fixed top row icon clipping on full grid pages by increasing top padding to `20dp` with `clipToPadding=false`.
  - Added full Snake D-pad navigation across all app grid rows and ends with smooth wraparound.
- **Universal YouTube Continue Watching**:
  - Unified playback tracking and history deduplication across official YouTube, SmartTube, and TizenTube.
- **Preferred Media Player Clean-Up**:
  - Removed buggy `space.nuvio.nativelegacy` from player discovery and package queries.

---

## [v3.9.2.1] - 2026-11-12
### Fixed
- Fixed all apps pinning to dock on app update.

---

## [v3.9.1] - 2026-11-11
### Added, Fixed & Enhanced (vs v3.9)
- **Minimalist Continue Watching & Strict App Filtering**:
  - Added **Minimalist** Continue Watching card style (`282×64dp` horizontal glass pill matching `tvOS-Launcher`) alongside **Classic** under `Streamer settings → Continue watching → Card style`.
  - On **Page 1**, Minimalist Continue Watching dynamically filters to only the currently focused dock app; on **Page 2**, it displays all active Continue Watching items in a single row with full D-pad traversal (`App dock ↔ CW cards ↔ Discover catalogs`).
  - Kept **Live lyrics on homescreen** enabled when Minimalist CW is selected on Page 1.
  - Strictly filtered `Continue watching apps` to streaming / Watch Next apps (+ official YouTube), excluding file managers, launchers, and system utilities.
  - Added a master **Continue watching** on/off toggle as the top row inside `Continue watching`, synchronized with `Turn features on/off`.
- **Clean Wallpaper Clarity, Full Cinema Alignment & Top-Bar Navigation**:
  - Removed the top shadow/white haze from custom photos, folder wallpapers, and Bing Daily wallpapers; fixed pre-decode raw-bitmap luminance sampling order so dark wallpapers stay dark; and defaulted `Appearance → Photo fade` to **Off** (keeping the top area clear even when bottom dock fade is enabled).
  - Anchored **TMDB Cinema** artwork, metadata, and a narrower `1280`-wide left-aligned synopsis block cleanly to the top-right below the top bar and focused icon label.
  - Clicking the top-bar **Wi-Fi** icon (when Music is off) now opens Android Wi-Fi Settings directly with matching luminance icon styling and D-pad focus return; long-pressing **Quick Settings** opens Android System Settings and preserves focus on the Quick Settings icon on `Back`.
  - Kept **Bluetooth** permanently enabled across the top icon bar and Quick Settings (removed the toggle and dead Quick Settings layout branches).
- **Centralized Feature Toggles (`Turn features on/off`)**:
  - Renamed and moved **Turn features on/off** directly above **Set launcher as default** in Main Settings, with master switches for **Calendar** (halting background refreshes & collapsing its top-bar gap when off), **Music**, **Continue watching**, and **Discover catalogs**.
- **Expanded Nuvio Player Variants & Deep-Link Resolution**:
  - Added individual detection, icon/label resolution, duplicate-label disambiguation, and scheme-aware launch resolution (`nuvio://`, `nuvio-legacy://`, `stremio://`, and launcher fallback) across all 7 supported Nuvio variants (`com.nuvio.tv`, `com.nuvio.app`, `com.nuvio.tv.test`, `com.nuvio.tv.launcher`, `com.manitux.nuviotr`, `com.robbdeeze.nuviotv`, and `com.nuvio.tv.lite`) under `Discover catalogs → Preferred media player`.

---

## [v3.7] - 2026-11-08
### Added & Enhanced
- **Streamer Music Settings & Live Lyrics Control**:
  - Restructured Settings into dedicated "Streamer music" menu with independent sub-toggles:
    - Master **Streamer Music** toggle (On/Off).
    - **Live lyrics on homescreen** toggle (On/Off) — dynamically visible *only* when the master Streamer Music toggle is ON.
    - Clean `DPAD_BACK` navigation returning directly to the main Settings menu.
- **Homescreen Live Lyrics & Seamless Dynamic Layout**:
  - Floating live lyrics layover positioned directly below the Now Playing pill.
  - Synchronous immediate teardown in `SyncedLyricsEngine.clearImmediate()` canceling background network calls and ticks, eliminating previous song lyric flash during track transitions.
  - Wired instant lyric teardown on `Player.STATE_ENDED` in `MusicPlaybackManager`.
  - Guarded homescreen live lyrics visibility strictly with `musicPlaybackManager.isPlaying`, keeping lyrics hidden when audio is paused, ended, or buffering.
- **Rock-Solid Homescreen Dock Stability**:
  - Converted `syncHomeGapWithTopBar` to synchronous zero-latency sizing (`animate = false`).
  - Removed conflicting `android:animateLayoutChanges="true"` on `clockContainer`.
  - The app dock remains mathematically locked at 416dp without jitter or displacement when songs end or change.
- **Interactive Three-Segment Now Playing Pill**:
  - Redesigned the Now Playing pill into three independently focusable segments:
    - **Song Info** (Artwork + Marquee Title): click opens the full Streamer Music player overlay.
    - **Play/Pause button**: click toggles playback instantly.
    - **Next Song button**: click skips to next track with instant lyric reset.
  - Maintained artwork aspect ratio within pill rounded borders without overflowing padding when in focus.
- **Bidirectional DPAD Navigation Matrix on Homescreen**:
  - **All Apps icon (`allAppsButton`) DPAD_LEFT**:
    - With Cricket score and Now Playing pills both showing -> navigates to Cricket score pill.
    - With only Cricket score pill showing -> navigates to Cricket score pill.
    - With only Now Playing pill showing -> navigates to Now Playing pill (song title).
    - With neither pill showing -> stays locked on All Apps icon.
  - **Cricket Score pill**:
    - `DPAD_RIGHT` navigates directly to the All Apps icon.
    - `DPAD_LEFT` & `DPAD_UP` remain on the Cricket score pill.
    - `DPAD_DOWN` navigates to Now Playing pill (if visible) or app dock.
  - **Now Playing Pill**:
    - With Cricket score pill ON: `DPAD_UP` from all pill segments -> Cricket score pill; `DPAD_LEFT` from song title -> Cricket score pill; `DPAD_RIGHT` from Next button -> All Apps icon; `DPAD_DOWN` -> app dock.
    - With Cricket score pill OFF: `DPAD_UP` -> stays on pill; `DPAD_LEFT` from song title -> stays on song title; `DPAD_RIGHT` from Next button -> All Apps icon; `DPAD_DOWN` -> app dock.
- **All Apps Section Grid Refinement**:
  - Optimized grid to maximum 6 app icons per row (`columnCount="6"`) with balanced card dimensions, generous padding, and no clipping on `1.08x` focus scale.
- **Quick Settings & LocalSend Remote Controls**:
  - Added dedicated Now Playing tile in Quick Settings showing thumbnail artwork, marquee title, artist, live play/pause state, and playback duration progress bar.
  - Added remote HOME button dismissal for the LocalSend modal in Quick Settings, cleanly closing the transfer view and returning to homescreen.
- **Universal TV Screensaver & Ambient Integration**:
  - Fixed Ambient screensaver launch in Quick Settings on Android 14 / Google TV via hidden framework reflection (`Sandman.startDreamByUser()`), with fallback to ambient dream packages and system dream settings.
  - Fixed screensaver preference synchronization on Android 11: direct launcher intent for `com.android.systemui.Somnambulator`, internal `ScreensaverActivity` for local photos and gallery paintings, and correct system default fallback.
- **Android TV & Google TV Default Launcher Auto-Persistence Across Builds**:
  - Added stock Android TV launcher `com.google.android.tvlauncher` to `ALLOWED_PACKAGES` and `isTvHome()` detection in `HomeOverride`.
  - Added `android:priority="3"` to `CATEGORY_HOME` intent-filter in `AndroidManifest.xml` to outrank stock Android TV (`priority="2"`).
  - Self-healing `ensureHomeService()` automatically re-arms accessibility and clears `mCrashedServices` on every package update (`ACTION_MY_PACKAGE_REPLACED`).
- **Autoplay Watchdog on Playback Stalls**:
  - Added 10-second watchdog timer in `MusicPlaybackManager` that auto-skips to the next track if an audio stream stalls or fails to load.
- **Streamer Music UI Loaders**:
  - Indeterminate loader spinner displayed during YouTube Music search queries until search results are ready.
  - Indeterminate loader spinner displayed when selecting a search result song or quick mix until playback initializes.

---

## [v3.6.2] - 2026-11-07
### Fixed & Hardened
- **Trailer Playback Media3 HLS Support**:
  - Added `androidx.media3:media3-exoplayer-hls:1.5.1` dependency and ProGuard keep rules for `androidx.media3.exoplayer.hls.**`, resolving `ClassNotFoundException: androidx.media3.exoplayer.hls.HlsMediaSource$Factory` during HLS trailer stream playback.
- **TV Calendar Unaired Episode Watched Button Visibility**:
  - Conditioned `calendarWatchedBtn.visibility` on whether the episode has actually aired (`airTimestamp <= System.currentTimeMillis() || dateStr <= todayStr`). Future and unaired episodes strictly hide the "Mark watched" button.
- **Calendar Disabled "No Player Installed" State**:
  - When no supported third-party player (Stremio, Nuvio, Wuplay, VLC) is installed on the system, the calendar play button displays "No player installed" in a disabled, non-clickable, non-focusable, dimmed state (`alpha = 0.4f`), perfectly mirroring homescreen catalog behavior.
  - Hardened D-pad focus routing on adjacent action buttons (`Trailer`, `Mark watched`, and episode cards) to cleanly bypass the disabled play button back to the active episode card.
- **Streamer Files Multi-Select Direct DPAD_LEFT to Clear**:
  - In `FileAdapter`, when multi-selection mode is active, pressing `DPAD_LEFT` on any selected or focused file/folder from any column in the list or grid immediately navigates directly to the `Clear` button (`clearActionButton.requestFocus()`), preventing focus from jumping to other folders.
- **Streamer Music UI & Search Results Loading Spinner**:
  - Added centered, elevated (30dp) indeterminate loader spinner (`musicLoadingSpinner`) on Streamer Music UI and Search Results UI. Displays immediately when a mix or search result track is selected, maintaining user context until playback initializes (`isPlaying == true`), and cleanly dismissing on playback start or error.
- **Music Search Popup Dismissal on HOME**:
  - Retained active dialog reference `musicSearchDialog` and intercepted `KeyEvent.KEYCODE_HOME` so pressing the remote HOME button while the search bar popup is open cleanly dismisses the popup and resets the homescreen.
  - Added explicit dialog dismissal hooks in `resetToHomePage1()`, `onPause()`, `onStop()`, and `closeMusicOverlay()`.
- **Music Search Results "New Search" Navigation**:
  - Repositioned `musicBtnEditSearch` ("New Search") adjacent to the dedicated search results Now Playing banner and wired bidirectional D-pad focus: pressing `DPAD_LEFT` from the Now Playing pill focuses "New Search", and pressing `DPAD_RIGHT` from "New Search" returns focus to the Now Playing pill.
- **Search Results Retention on Track Change**:
  - In `MusicPlaybackListener.onTrackChanged`, prevented forced navigation to the playback UI (`showMusicPlayingState()`) while browsing search results (`musicSearchResultsLayout.visibility == View.VISIBLE`), updating only the top Now Playing banner while preserving the user's active search results view.
- **Streamer Music Exit Transition Polish**:
  - Synchronized exit animation and dock gap recalculation in `closeMusicOverlay()` to completely eliminate visual dock displacement when returning from music playback to the homescreen.
- **Quick Settings Music Pill Immediate Removal**:
  - Clearing audio queue (`musicPlaybackManager.clearQueue()`) and updating quick settings tiles immediately when music support is disabled in Streamer Settings.
- **LocalSend File Picker & Multi-Select Integration**:
  - Instant single-click selection in document picker mode, dedicated action bar with `[ OK ($count) ]` button, and explicit long-press entry for multi-item transfers.

---

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
