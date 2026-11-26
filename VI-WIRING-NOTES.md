# Vietnamese UI wiring — notes (vi-work, NOT reviewed for push)

Branch: `vi-work` (head `9cdfa00`). Changes are in the working tree only:
nothing is committed and nothing is pushed. Gradle was NOT run (no Android
SDK in this environment), so this is a source-level wiring pass that still
needs `./gradlew :app:assembleDebug` on a machine with the SDK.

## What was done

- `app/src/main/res/values/strings.xml` and `app/src/main/res/values-vi/strings.xml`:
  28 keys before, **147 keys each now (119 new)**. Key sets match exactly
  (verified by script), both files parse as XML, and every `%N$` format
  argument matches between EN and VI.
- The 6 in-scope Kotlin files now use `stringResource(R.string.<key>)`
  (176 call sites, 145 distinct keys referenced, all of which exist).
- `AppTab` (`NRSuiteApp.kt`) changed from `label: String` to
  `@StringRes labelRes: Int`; its only two uses (bottom navigation label and
  content description in `NRSuiteContent.kt`) now resolve the label.
- New `@Composable internal fun categoryDisplayName(category: String)`
  (`NRSuiteApp.kt`): maps a canonical category key to its display string.
  The key itself is NEVER translated, because it is used to filter modules
  (`modules.filter { it.category == selectedCategory }`), as the
  `onOpenCategory` argument, and by `categoryColor()` (which matches on the
  English names). Unknown categories fall through unchanged.

## Verification (all passed)

- `xml.etree` parses both `strings.xml` files; key lists identical, no empty
  VI values, no `%N$` format-argument mismatch.
- Every `R.string.*` referenced in the 6 files exists in both XML files.
- Diff sweep of the touched lines: no targeted hardcoded English string
  remains (remaining hits are canonical keys, chip names, or comments).
- `git diff --stat` for the touched paths:

```
 .../java/com/swp81x/nrsuite/ui/DeviceScreen.kt     |  53 ++++-----
 .../main/java/com/swp81x/nrsuite/ui/HomeScreen.kt  |  73 +++++++------
 .../main/java/com/swp81x/nrsuite/ui/LogsScreen.kt  |  45 +++++---
 .../main/java/com/swp81x/nrsuite/ui/NRSuiteApp.kt  |  34 +++++-
 .../java/com/swp81x/nrsuite/ui/NRSuiteContent.kt   |  62 ++++++-----
 .../java/com/swp81x/nrsuite/ui/SettingsScreen.kt   | 120 ++++++++++----------
 app/src/main/res/values-vi/strings.xml             | 121 ++++++++++++++++++++-
 app/src/main/res/values/strings.xml                | 121 ++++++++++++++++++++-
 8 files changed, 460 insertions(+), 169 deletions(-)
```

## Files changed (this pass only)

- `app/src/main/java/com/swp81x/nrsuite/ui/HomeScreen.kt`
- `app/src/main/java/com/swp81x/nrsuite/ui/NRSuiteContent.kt`
- `app/src/main/java/com/swp81x/nrsuite/ui/NRSuiteApp.kt`
- `app/src/main/java/com/swp81x/nrsuite/ui/SettingsScreen.kt`
- `app/src/main/java/com/swp81x/nrsuite/ui/DeviceScreen.kt`
- `app/src/main/java/com/swp81x/nrsuite/ui/LogsScreen.kt`
- `app/src/main/res/values/strings.xml`
- `app/src/main/res/values-vi/strings.xml`

Note: `README_VI.md` and `docs/index.html` also show as modified in
`git status`, but they were NOT touched by this pass (concurrent landing-page
work in the same checkout). Do not include them in this review by mistake.

## Keys added (119)

| Key | English | Vietnamese |
| --- | --- | --- |
| `status_unpaired` | Unpaired | Chưa ghép nối |
| `status_connected` | Connected | Đã kết nối |
| `status_disconnected` | Disconnected | Đã ngắt kết nối |
| `status_disconnecting` | Disconnecting… | Đang ngắt kết nối… |
| `status_error_with_message` | Error: %1$s | Lỗi: %1$s |
| `status_permission_granted` | Permission granted | Đã cấp quyền |
| `status_permission_required` | Permission required | Cần cấp quyền |
| `status_permission_requested` | Permission requested | Đã yêu cầu quyền |
| `status_connection_failed` | Connection failed | Kết nối thất bại |
| `status_not_connected` | not connected | chưa kết nối |
| `settings_board_unknown` | Not connected / unknown | Chưa kết nối / không rõ |
| `status_selected` | Selected | Đã chọn |
| `status_preparing` | Preparing… | Đang chuẩn bị… |
| `status_flashing` | Flashing… | Đang nạp firmware… |
| `status_whitelist` | Whitelist | Danh sách trắng |
| `status_blacklist` | Blacklist | Danh sách đen |
| `action_cancel` | Cancel | Hủy |
| `action_clear` | Clear | Xóa |
| `action_delete` | Delete | Xóa |
| `action_save` | Save | Lưu |
| `action_change` | Change | Đổi |
| `action_select` | Select | Chọn |
| `action_grant_permission` | Grant permission | Cấp quyền |
| `action_choose_folder` | Choose folder | Chọn thư mục |
| `action_choose_root_folder` | Choose root folder | Chọn thư mục gốc |
| `action_choose_firmware` | Choose firmware .bin | Chọn file firmware .bin |
| `action_flash_firmware` | Flash firmware | Nạp firmware |
| `action_flash_now` | Flash now | Nạp ngay |
| `action_manage_device` | Manage device | Quản lý thiết bị |
| `action_open_github` | Open GitHub | Mở GitHub |
| `action_add_rule` | Add rule | Thêm quy tắc |
| `action_search_modules` | Search modules… | Tìm mô-đun… |
| `action_copy_log` | Copy log | Sao chép nhật ký |
| `action_export_log` | Export log | Xuất nhật ký |
| `action_clear_log` | Clear log | Xóa nhật ký |
| `action_copy_history` | Copy history | Sao chép lịch sử |
| `action_export_history` | Export history | Xuất lịch sử |
| `action_clear_history` | Clear history | Xóa lịch sử |
| `action_delete_oui_rule` | Delete OUI rule | Xóa quy tắc OUI |
| `action_update_database` | Update database | Cập nhật cơ sở dữ liệu |
| `category_hid` | HID | HID |
| `category_ir` | IR | IR |
| `category_rf` | RF | RF |
| `category_rfid` | RFID | RFID |
| `category_all` | All | Tất cả |
| `category_modules_one` | 1 module | 1 mô-đun |
| `category_modules_count` | %1$d modules | %1$d mô-đun |
| `category_modules_none` | No modules | Không có mô-đun |
| `home_attached_usb_devices` | Attached USB devices | Thiết bị USB đã gắn |
| `home_no_usb_devices` | No supported USB serial devices found. Plug in your NRSuite ESP32 over OTG. | Không tìm thấy thiết bị USB serial được hỗ trợ. Hãy cắm ESP32 NRSuite qua OTG. |
| `home_recently_used` | Recently used | Dùng gần đây |
| `home_no_device_connected` | No device connected | Chưa kết nối thiết bị |
| `home_plug_in_device` | Plug in your NRSuite ESP32 with a USB OTG cable, then grant USB permission. | Cắm ESP32 NRSuite bằng cáp USB OTG, rồi cấp quyền USB. |
| `home_connecting_device` | Connecting to device… | Đang kết nối tới thiết bị… |
| `home_waiting_handshake` | Waiting for the USB serial handshake and PING response. | Đang chờ bắt tay USB serial và phản hồi PING. |
| `home_connected_to` | Connected to %1$s | Đã kết nối tới %1$s |
| `home_connection_problem` | Connection problem | Sự cố kết nối |
| `oui_not_downloaded_title` | OUI/vendor database not downloaded | Chưa tải cơ sở dữ liệu OUI/nhà sản xuất |
| `oui_downloading_title` | Updating OUI/vendor database… | Đang cập nhật cơ sở dữ liệu OUI/nhà sản xuất… |
| `oui_error_title` | OUI/vendor database download failed | Tải cơ sở dữ liệu OUI/nhà sản xuất thất bại |
| `oui_not_downloaded_message` | Vendor names and OUI-based signals will be unavailable until this is downloaded. | Tên nhà sản xuất và các tín hiệu dựa trên OUI sẽ không khả dụng cho đến khi tải xong. |
| `oui_downloading_message` | Downloading the IEEE OUI registry. Vendor names and OUI-based signals will be available when it finishes. | Đang tải sổ đăng ký OUI của IEEE. Tên nhà sản xuất và các tín hiệu dựa trên OUI sẽ khả dụng khi tải xong. |
| `oui_download_database` | Download database | Tải cơ sở dữ liệu |
| `oui_retry_download` | Retry download | Thử tải lại |
| `oui_not_downloaded` | Not downloaded. | Chưa tải. |
| `oui_downloading` | Downloading… | Đang tải… |
| `oui_downloading_progress` | Downloading… %1$d%% | Đang tải… %1$d%% |
| `oui_ready` | %1$d OUI prefixes · updated %2$s | %1$d tiền tố OUI · cập nhật %2$s |
| `dialog_root_dir_title` | NRSuite root directory | Thư mục gốc NRSuite |
| `dialog_oui_title` | MAC vendor database | Cơ sở dữ liệu nhà sản xuất MAC |
| `dialog_action_blocked_title` | Action blocked | Hành động bị chặn |
| `dialog_flash_title` | Flash firmware? | Nạp firmware? |
| `dialog_flash_text` | This will write the selected firmware on %1$s and reboot it. Do not unplug during the process. | Thao tác này sẽ ghi firmware đã chọn lên %1$s và khởi động lại thiết bị. Không rút cáp trong quá trình nạp. |
| `dialog_delete_rule_title` | Delete rule? | Xóa quy tắc? |
| `dialog_delete_rule_text` | Remove the rule for %1$s? | Xóa quy tắc cho %1$s? |
| `snackbar_device_detected` | %1$s detected - tap to connect | Đã phát hiện %1$s - chạm để kết nối |
| `module_unavailable_device_needed` | No device connected. Connect your NRSuite ESP32 to run %1$s. | Chưa kết nối thiết bị. Hãy kết nối ESP32 NRSuite để chạy %1$s. |
| `module_unavailable_not_supported` | %1$s is not supported by the connected ESP32%2$s. | %1$s không được ESP32 đã kết nối hỗ trợ%2$s. |
| `module_unavailable_firmware_required` | The connected firmware is missing the required feature for %1$s. | Firmware đã kết nối thiếu tính năng cần thiết cho %1$s. |
| `module_unavailable_oui_required` | Download the OUI/vendor database before using %1$s. | Hãy tải cơ sở dữ liệu OUI/nhà sản xuất trước khi dùng %1$s. |
| `module_unavailable_generic` | %1$s is not available yet. | %1$s chưa khả dụng. |
| `title_flasher` | Flasher | Nạp firmware |
| `title_wpa_cracker` | WPA Cracker | Bẻ khóa WPA |
| `title_devices` | Devices | Thiết bị |
| `device_no_usb_devices` | No supported USB serial devices found. Plug in an ESP32 via OTG and tap Refresh. | Không tìm thấy thiết bị USB serial được hỗ trợ. Hãy cắm ESP32 qua OTG rồi bấm Làm mới. |
| `device_connection_title` | Connection | Kết nối |
| `device_hardware_info` | Hardware info | Thông tin phần cứng |
| `logs_tab_runtime` | Runtime | Thời gian chạy |
| `logs_tab_history` | History (%1$d) | Lịch sử (%1$d) |
| `logs_session_log` | Session log | Nhật ký phiên |
| `logs_session_history` | Session history | Lịch sử phiên |
| `logs_filter_errors` | Errors | Lỗi |
| `logs_filter_success` | Success | Thành công |
| `logs_empty_runtime` | No runtime logs yet | Chưa có nhật ký thời gian chạy |
| `logs_empty_runtime_hint` | Connect a device or run a module to see live log output. | Kết nối thiết bị hoặc chạy một mô-đun để xem nhật ký trực tiếp. |
| `logs_empty_history` | No sessions yet | Chưa có phiên nào |
| `logs_empty_history_hint` | Run a module to record your first session. | Chạy một mô-đun để ghi lại phiên đầu tiên. |
| `settings_appearance` | Appearance | Giao diện |
| `settings_developer` | Developer | Nhà phát triển |
| `settings_about` | About | Giới thiệu |
| `settings_oui_rules_title` | Rogue AP Detector — OUI rules | Phát hiện AP giả mạo — Quy tắc OUI |
| `settings_oui_no_rules` | No rules yet. | Chưa có quy tắc nào. |
| `settings_oui_prefix_label` | OUI prefix (AA:BB:CC) | Tiền tố OUI (AA:BB:CC) |
| `settings_rule_label` | Label | Nhãn |
| `settings_flash_step_target` | 1. Target device | 1. Thiết bị đích |
| `settings_flash_step_image` | 2. Firmware image | 2. File firmware |
| `settings_flash_step_review` | 3. Review | 3. Kiểm tra lại |
| `settings_flash_target` | Flash target | Thiết bị nạp |
| `settings_no_flash_device` | No supported USB serial device found. Plug in the board and tap Refresh. | Không tìm thấy thiết bị USB serial được hỗ trợ. Hãy cắm board rồi bấm Làm mới. |
| `settings_no_firmware_selected` | No merged firmware .bin selected | Chưa chọn file firmware .bin đã gộp |
| `settings_select_device_first` | Select a device above to choose firmware. | Hãy chọn thiết bị ở trên để chọn firmware. |
| `settings_target_chip` | Target chip | Chip đích |
| `settings_advanced_options` | Advanced options | Tùy chọn nâng cao |
| `settings_hide_advanced_options` | Hide advanced options | Ẩn tùy chọn nâng cao |
| `settings_skip_autoreset` | Skip auto-reset (already in ROM bootloader) | Bỏ qua tự khởi động lại (đã ở chế độ ROM bootloader) |
| `settings_current_version` | Current version: %1$s | Phiên bản hiện tại: %1$s |
| `settings_detected_board` | Detected board: %1$s | Board đã phát hiện: %1$s |
| `settings_flash_target_hint` | Select the board to flash and grant USB permission. | Chọn board cần nạp và cấp quyền USB. |
| `settings_firmware_size` | %1$s  (%2$d KB) | %1$s  (%2$d KB) |

## Strings deliberately left hardcoded (and why)

- **Category / module keys**: `CategorySpec.name`, `ModuleCardSpec.category`,
  `ModuleCardSpec.id`, `statusLabel` values (`"Unavailable"`, `"Device needed"`,
  `"Not supported"`, `"Firmware required"`, `"OUI DB required"`), chip names
  (`"ESP32-C3"`, `"ESP32-S3"`, …), `"Not configured"` — compared with `==` /
  used as map/filter keys in code and in `categoryColor()`. Translating them
  would break filtering, availability colours, and the module-unavailable
  `when (module.statusLabel)` branches. Only their *display* rendering is
  translated (via `categoryDisplayName` / `module_unavailable_*` keys).
- **Module titles and descriptions** in the `modules` list (`NRSuiteApp.kt`,
  e.g. `"WiFi Scan"`, `"Rogue AP Detector"`, `"Packet Sniffer"`) — display-only
  data, but translating all ~25 modules is a separate pass (see below).
- **Wire protocol / technical readouts**: `PING` (kept inside the translated
  `home_waiting_handshake` sentence), `STATUS` command mention, `fw:` /
  `id:` / `Chip:` / `Firmware:` / `Device ID:` / `Transport: USB serial / CDC`
  lines, `"Flash offset: 0x0"`, `"USB"` log filter, `entry.level.name`,
  `"unknown"` fallbacks for chip/firmware/device-id. These are identifiers or
  `key: value` technical readouts, not prose.
- **Long explanatory body paragraphs** in `SettingsScreen.kt` (root-directory
  hint, skip-auto-reset explanation, flash warning, OUI-rules hint, OUI
  database hint, Appearance/Developer/About notes), the root-directory and
  OUI dialog bodies in `NRSuiteContent.kt`, and the Firmware card body in
  `DeviceScreen.kt` — left for a follow-up pass to keep this diff reviewable.
- **Dynamic / ViewModel-owned strings**: `ConnectionState.Failed.message`,
  `OuiDatabaseStatus.Error.message`, `firmwareFlashStatus`, `actionError`
  message, log entry `tag`/`message`, history `summary`, `entry.displayName`,
  `firmwareFileName` — runtime data, not literals.
- **Fallback display names**: `"NRSuite device"` (connected-chip fallback in
  `home_connected_to`) and `"the device"` (flash-confirm fallback) stay
  English inside otherwise translated sentences.
- **Cosmetic only**: `DeviceScreen`/`DeviceRow` `"Connecting..."` now renders
  as the existing `status_connecting` (`"Connecting…"`, single ellipsis
  character) instead of three periods; `"Disconnecting..."` likewise renders
  as `status_disconnecting` (`"Disconnecting…"`).
- **Non-composable contexts**: the device-detected snackbar (`NRSuiteContent`)
  and the log/history share-chooser titles (`LogsScreen`) cannot call
  `stringResource` inside `LaunchedEffect`/coroutine/`onClick`, so the format
  strings are resolved once in composable scope (`deviceDetectedFormat`,
  `connectLabel`, `exportLogLabel`, `exportHistoryLabel`) and reused.

## What still needs translating

- **Module screens (~55 files, not touched)**: `WifiScanScreen`, `SniffScreen`,
  `BeaconScreen`, `DeauthScreen`, `PortalScreen`, `EvilTwinScreen`,
  `WpsPixie*`, `WpaCrackerScreen`, `Ble*`, `DuckyEditorScreen`, `BadUsbScreen`,
  `Ir*`, `MeshScreen`, `StorageScreen`, `SerialMonitorScreen`, `Terminal*`,
  `CredentialManagerScreen`, `MacLookupScreen`, `ChannelAnalyzerScreen`,
  `ProbeMonitorScreen`, `HiddenApScreen`, `RogueApScreen`, `DeauthDetectorScreen`,
  `ClientPresenceScreen`, `TrackerDetectorScreen`, `WpsLockDetectorScreen`,
  `WpsAttemptDetectorScreen`, `PinBruteforceScreen`, `BlePinBruteforceScreen`,
  `Pcap22000Screen`, `Ld2450Screen`, `Rfid*`, `SensorConfigScreen`,
  `WifiConfigScreen`, `StartupScreen`, plus shared components in `ui/components/`
  (`ModuleCard`, `ModuleStatusCard`, `NetworkStatusBadge`, `NetworkTargetRow`,
  `SavedItemDropdown`, `SavedScriptPicker`, `HtmlUploadSection`, …) and
  `ModuleAvailability.kt` (`ModuleScreenScaffold` banner).
- **Module titles/descriptions** in the `modules` list in `NRSuiteApp.kt`
  (25 modules) — needs a key per title/description, or a title-key lookup in
  `ModuleCard`.
- **Left-over body paragraphs** listed above (Settings, dialogs, DeviceScreen
  Firmware card).
- **Language switching itself**: the `language_*` keys exist, but no settings
  UI or `AppCompatDelegate.setApplicationLocales` wiring was added in this
  pass — today the app follows the system locale (`values-vi` applies when
  the device language is Vietnamese). A language picker in Settings is still
  to be built.
- **Build check still required**: `./gradlew :app:assembleDebug` and
  `:app:testDebugUnitTest` on a machine with Android SDK 36 / JDK 17.

---

# 1.0.0 rewire (branch `vi-1.0.0`, base upstream main `eff6968`)

Same 6 files, re-applied to the 1.0.0 sources. The string tables were NOT
changed in this pass: both `strings.xml` files still have 147 keys each,
key sets identical, no empty VI values, no `%N$` format-argument mismatch
(all re-verified by script after the rewire).

## What was done

- **176 `stringResource` call sites (172 lines), 113 distinct keys**, all of
  which exist in both XML files. 34 keys in the tables are still unused by
  these 6 files (flasher step keys, module titles/descriptions, `language_*`,
  `device_connection_title`, `device_hardware_info`, `logs_session_*`,
  `settings_*` hints, etc. — they belong to screens/sections not wired yet).
- Per file: `HomeScreen.kt` 42, `NRSuiteContent.kt` 23, `NRSuiteApp.kt` 17,
  `SettingsScreen.kt` 48, `DeviceScreen.kt` 24, `LogsScreen.kt` 18.
- Diff vs upstream HEAD for the 6 Kotlin files only:
  `212 insertions(+), 153 deletions(-)`.
- A `git apply --3way` of the beta.2 diff was tried first and abandoned:
  conflicts in all 6 files, plus mis-applied hunks (a bottom-navigation
  fragment spliced into the USB-attach-prompt loop in `NRSuiteContent.kt`).
  The rewire was then done manually from a full literal inventory of each
  1.0.0 file. `NRSuiteApp.kt` / `NRSuiteContent.kt` were resolved in the
  index (`git add`, no commit); the other four files were rewired from a
  clean `git checkout HEAD` of the merged versions.

## How 1.0.0 differs from the beta.2 reference pass

- **`AppTab` is HOME / DEVICES / TERMINAL / LOGS** (no MODULES tab), so the
  beta.2 `labelRes` refactor was NOT used. Instead
  `@Composable internal fun tabLabel(tab: AppTab)` in `NRSuiteApp.kt` maps
  HOME → `R.string.home`, DEVICES → `R.string.title_devices`,
  LOGS → `R.string.logs`; **TERMINAL stays `"Terminal"` — no key exists**.
  `categoryDisplayName(category: String)` is the same approach as beta.2;
  **"Sensors" has no key and falls through unchanged**.
- **The device-detected snackbar is gone in 1.0.0.** Device detection is via
  `UsbAttachPromptCard`; the beta.2 hoisted `deviceDetectedFormat` /
  `connectLabel` and the `suppressDeviceSnackbar` /
  `previousUnacknowledgedDevices` block do not exist here and were dropped.
  `switchActiveDeviceWithToast` was kept as-is; its Toast
  `"Active device: …"` has no key and stays hardcoded.
- **Flasher step keys do not match 1.0.0.** `settings_flash_step_target` /
  `_image` / `_review` ("1. Target device", "2. Firmware image",
  "3. Review") have no counterpart: the 1.0.0 flasher headings are
  unnumbered ("Target device" / "Firmware image"), so those keys stay
  unused. The flash-confirm dialog text maps exactly to
  `dialog_flash_text` (with the `"the device"` fallback kept hardcoded).
- **The 1.0.0 files are much larger and have new sections**: HomeScreen
  1,799 lines, NRSuiteContent 2,947 lines, SettingsScreen 1,783 lines —
  IR database, terminal shortcuts, firmware-flasher remote/local sources,
  OUI database section, app-update cards, `HardwareInfoCard`.
- **LogsScreen tabs** are `"Runtime (${logs.size})"` /
  `"History (${history.size})"`. History maps exactly to
  `logs_tab_history` (counted); Runtime has no counted key, so it is
  composed as `"${stringResource(R.string.logs_tab_runtime)} (${logs.size})"`.
  The share-chooser titles were hoisted again exactly as in beta.2
  (`exportLogLabel` / `exportHistoryLabel` resolved in composable scope);
  note the chooser title "Export logs" now renders as `action_export_log`
  ("Export log", singular) — a one-word English change from the 1.0.0 text.
- `HomeScreen` `CategoryCard` availability: the label is translated only
  when `statusLabel == "Unavailable"` → `status_unavailable`; the canonical
  key itself is never translated.
- `DeviceScreen` status `when` blocks map to `status_disconnected` /
  `status_disconnecting` / `status_connecting` / `status_connected` /
  `status_permission_requested` / `status_connection_failed` /
  `status_permission_granted` / `status_permission_required` /
  `status_flashing` / `action_disconnect` / `action_connect` /
  `action_refresh` / `action_cancel` / `action_clear` /
  `device_no_usb_devices`.
- `SettingsScreen` OUI section maps to `oui_not_downloaded` /
  `oui_downloading_progress` / `oui_ready` / `action_update_database` /
  `oui_download_database`; the OUI rule chips/badge map to
  `status_whitelist` / `status_blacklist`.

## Strings left hardcoded in 1.0.0 (no matching key — do not invent keys)

- **No key exists**: "Terminal", "Sensors", "Diagnosing…", "Switch active
  device", "App update available", "Browse all", "Recon" / "Attack" /
  "Defend" filter labels, "WiFi Config", "Sensor configuration" /
  "Sensor Config", "IR Remote Details", "WiFi config", "PCAP to.hs22000",
  "RFID config", "Sensor config", "All Modules", "Terminal Settings",
  "Terminal Shortcuts" / "Open shortcuts" / "Manage shortcuts" /
  "Run shortcut?" / "Run", "Back to IR Finder" / "Back to IR Remotes",
  "All modules", "Downloading ${it}%", "Update", "Retry",
  "Flashing firmware...", "Replug required", "Serial Monitor active",
  "Diagnosing...", "Ready to connect", "USB permission required",
  "Flashed", "Busy", "Waiting…", "Connected · inactive",
  "Connection diagnosis", "Switch", "No device attached",
  "Device attached, not connected", "Select a device below and tap Connect.",
  "Diagnosing connection...", "Checking USB permission…",
  "ATTACHED USB DEVICES" (case differs from the key), "+N more — open
  Devices to manage all", "No other USB devices attached.",
  "Clear runtime logs", "Export visible logs",
  "Search message, tag, or time", "Clear search",
  "Choose NRSuite root", "Rescan library", "Download Flipper-IRDB",
  IR-library status/body strings.
- **Wording differs from the nearest key, left as-is**: "Plug in your
  NRSuite ESP32 with a USB-OTG cable." (vs `home_plug_in_device`, which
  adds "then grant USB permission"); `logs_empty_runtime_hint` ("live
  output" vs the key's "live log output") — only the title
  `logs_empty_runtime` was wired.
- **Non-composable context**: `UpdateAvailableCard`'s default parameter
  `buttonLabel: String = "Download"` cannot call `stringResource` (default
  argument, not composable scope) — left hardcoded; the `when` that passes
  an explicit `buttonLabel` IS wired.
- **Canonical keys / protocol / technical**: category/module ids,
  `statusLabel` values used for filtering, chip names, MAC/BSSID/RSSI,
  BLE, USB, ESP32, "NRSuite device" / "the device" fallbacks — same rule
  as beta.2.

## Verification (all passed, 1.0.0 rewire)

- `xml.etree` parses both `strings.xml` files; 147 keys each, key sets
  identical, no empty VI values, no `%N$` format-argument mismatch.
- Every `R.string.*` referenced in the 6 files (113 distinct keys) exists
  in both XML files.
- Scope check: every `stringResource` call site sits inside a
  `@Composable` function (verified by inspection of the enclosing
  functions, including the nested local `fun isSerialMonitorActiveDevice`
  inside `NRSuiteContent`, and `HardwareInfoCard` in `DeviceScreen`);
  the only deliberate exception is the `UpdateAvailableCard` default
  parameter above, which was left hardcoded.
- Brace/paren balance is 0 in all 6 files; 0 conflict markers remain.
- Diff sweep of the added lines: the only remaining hardcoded English
  literals are intentional (comments, the "Terminal" / "Sensors"
  fallbacks, the canonical `"Unavailable"` key).
- Gradle was NOT run (no Android SDK in this environment). Nothing is
  committed and nothing is pushed; changes are in the working tree on
  branch `vi-1.0.0` for review. Note: `.github/workflows/release.yml`
  (signing-secret rename) and `app/build.gradle.kts` (versionCode 17 /
  versionName 1.0.0-vi.1) also show as modified in this checkout but were
  NOT touched by this rewire pass.
