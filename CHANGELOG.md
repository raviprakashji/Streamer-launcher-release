# Streamer Launcher - Changelog

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
