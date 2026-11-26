# NRSuite Android — Tiếng Việt

Bản Việt hóa của NRSuite Android 1.0.0-vi.1, fork từ [7wp81x/nrsuite-android](https://github.com/7wp81x/nrsuite-android). App Android điều khiển board ESP32 qua USB-OTG để nghiên cứu bảo mật Wi-Fi/BLE, không cần laptop, không cần root.

Trang giới thiệu tiếng Việt: https://khahdihdz.github.io/NRSuite-Android/

## Nguyên tắc

- Dịch tự nhiên, ưu tiên thuật ngữ kỹ thuật quen thuộc.
- Giữ nguyên tên giao thức, command, protocol, MAC/BSSID/RSSI, BLE, USB, ESP32 và các identifier trong code.
- Không thay đổi logic chức năng chỉ để dịch giao diện.
- Chuỗi giao diện được chuẩn bị trong Android resources (`app/src/main/res/values/strings.xml` và `app/src/main/res/values-vi/strings.xml`) để từng module có thể chuyển từ hard-code sang localization an toàn.

## Tính năng chính

- **Khảo sát Wi-Fi:** quét AP/kênh, SSID, BSSID, RSSI, bảo mật, WPS; phân tích kênh; bắt gói tin và xuất PCAP.
- **Tấn công (chỉ dùng khi được ủy quyền):** deauth, beacon, Evil Twin, Captive Portal, WPS Pixie Dust, bẻ handshake WPA ngoại tuyến (.22000).
- **Phòng vệ:** phát hiện AP giả mạo, deauth, tracker, client bất thường, khóa WPS.
- **BLE:** quét thiết bị, khảo sát GATT, BLE HID, phát hiện tracker.
- **Hồng ngoại:** remote Flipper .ir, thu/phát tín hiệu, tìm remote, IR Jammer có giám sát.
- **Terminal:** SSH tới Termux, khóa Ed25519/RSA, nhiều tab, phím tắt.
- **Firmware:** nạp firmware NRSuite cho ESP32/S2/S3/C3/C5 trong app, Serial Monitor, quản lý bộ nhớ.
- **Mesh (ESP-NOW):** đang thử nghiệm — cấp phát khóa nhóm, bầu master, heartbeat, báo cáo cảm biến.

## Yêu cầu

- Điện thoại Android 8.0 trở lên (API 26), hỗ trợ USB-OTG.
- Board ESP32 đã nạp firmware NRSuite. Bản firmware tiếng Việt: [NRSuite-firmware v1.0.0-vi.1](https://github.com/khahdihdz/NRSuite-firmware/releases/tag/v1.0.0-vi.1).
- Cáp USB-OTG.

## Cài đặt

### Từ mã nguồn

```bash
git clone https://github.com/khahdihdz/NRSuite-Android.git
cd NRSuite-Android
./gradlew assembleDebug
```

APK debug ở `app/build/outputs/apk/debug/app-debug.apk`. Cần Android Studio Ladybug trở lên, JDK 17, Android SDK API 36.

### Bản phát hành

Tải APK từ [Releases](https://github.com/khahdihdz/NRSuite-Android/releases), kiểm tra SHA256 trong ghi chú release trước khi cài.

## Trạng thái việt hóa

- Landing page tiếng Việt (`docs/index.html`) và workflow GitHub Pages (`.github/workflows/pages.yml`): xong.
- Bảng chuỗi tiếng Việt (`values-vi/strings.xml`): 147 khóa, đang được nối vào Trang chính và các màn hình chính. Các màn hình module còn lại vẫn hiển thị tiếng Anh cho tới khi được chuyển sang resource.
- README tiếng Anh gốc vẫn giữ nguyên; file này là bản tiếng Việt.

## Chỉ dùng khi được ủy quyền

NRSuite chỉ dùng cho nghiên cứu bảo mật trên mạng, thiết bị và hệ thống mà bạn sở hữu hoặc có quyền kiểm tra bằng văn bản. Xem `LEGAL.md`.
