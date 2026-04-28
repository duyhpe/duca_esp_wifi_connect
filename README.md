# Kết nối Wi-Fi ESP32
Thành phần này hỗ trợ kết nối Wi-Fi cho thiết bị.

Đầu tiên, nó cố gắng kết nối với mạng Wi-Fi bằng cách sử dụng thông tin đăng nhập được lưu trữ trong flash. Nếu điều này không thành công, nó sẽ khởi động một điểm truy cập và một máy chủ web để cho phép người dùng kết nối với mạng Wi-Fi.

URL để truy cập máy chủ web là `http://192.168.4.1`.
### Ảnh chụp màn hình: Cấu hình Wi-Fi

<img src="assets/ap_v3.png" width="320" alt="Wi-Fi Configuration">

### Ảnh chụp màn hình: Tùy chọn nâng cao

<img src="assets/ap_v3_advanced.png" width="320" alt="Advanced Configuration">

## Nhật ký thay đổi: v3.1.0

- Gọi lại sự kiện hiện bao gồm một tham số `data` bổ sung để biết thêm thông tin.

- Sự kiện bị ngắt kết nối hiện cung cấp mã lý do ngắt kết nối thông qua tham số `data`.

- Điều này cho phép các ứng dụng xử lý các tình huống ngắt kết nối khác nhau một cách thích hợp.
## Nhật ký thay đổi: v3.0.0

- Đã thêm lớp WifiManager để quản lý kết nối WiFi thống nhất.

- Các lớp DnsServer và WifiConfigurationAp được cải thiện để xử lý tài nguyên tốt hơn.

- Cập nhật HTML cho thông báo cấu hình thành công để sử dụng điểm cuối thoát thay vì khởi động lại.

- Tăng cường xử lý lỗi và quản lý trạng thái trong WifiStation.

- Dọn dẹp mã không sử dụng và cải thiện độ an toàn luồng trên các thành phần.
## Nhật ký thay đổi: v2.6.0

- Thêm hỗ trợ cho chế độ ESP32C5 5G.

## Nhật ký thay đổi: v2.4.0

- Thêm ngôn ngữ ja / zh-TW.

- Thêm tab nâng cao.

- Thêm tiêu đề "Kết nối: đóng" để lưu các ổ cắm đang mở.

## Nhật ký thay đổi: v2.3.0

- Thêm hỗ trợ cho yêu cầu ngôn ngữ.

## Nhật ký thay đổi: v2.2.0

- Thêm hỗ trợ cho ESP32 SmartConfig (ESPTouch v2)

## Nhật ký thay đổi: v2.1.0

- Cải thiện logic kết nối Wi-Fi.

## Nhật ký thay đổi: v2.0.0

- Thêm hỗ trợ cho nhiều quản lý Wi-Fi SSID.

- Tự động chuyển sang mạng Wi-Fi tốt nhất.

- Cổng Captive cho cấu hình Wi-Fi.

- Hỗ trợ nhiều ngôn ngữ (tiếng Anh, tiếng Trung).

## Cấu hình

Thông tin đăng nhập Wi-Fi được lưu trữ trong flash dưới không gian tên "wifi".
The keys are "ssid", "ssid1", "ssid2" ... "ssid9", "password", "password1", "password2" ... "password9".

## Cách sử dụng

```cpp
#include <wifi_manager.h>
#include <ssid_manager.h>

// Initialize the default event loop
ESP_ERROR_CHECK(esp_event_loop_create_default());

// Initialize NVS flash for Wi-Fi configuration
esp_err_t ret = nvs_flash_init();
if (ret == ESP_ERR_NVS_NO_FREE_PAGES || ret == ESP_ERR_NVS_NEW_VERSION_FOUND) {
    ESP_ERROR_CHECK(nvs_flash_erase());
    ret = nvs_flash_init();
}
ESP_ERROR_CHECK(ret);

// Get the WifiManager singleton
auto& wifi_manager = WifiManager::GetInstance();

// Initialize with configuration
WifiManagerConfig config;
config.ssid_prefix = "ESP32";  // AP mode SSID prefix
config.language = "zh-CN";     // Web UI language
wifi_manager.Initialize(config);

// Set event callback to handle WiFi events
// The callback receives the event type and optional data (e.g., disconnect reason)
wifi_manager.SetEventCallback([](WifiEvent event, const std::string& data) {
    switch (event) {
        case WifiEvent::Scanning:
            ESP_LOGI("WiFi", "Scanning for networks...");
            break;
        case WifiEvent::Connecting:
            ESP_LOGI("WiFi", "Connecting to network...");
            break;
        case WifiEvent::Connected:
            ESP_LOGI("WiFi", "Connected successfully!");
            break;
        case WifiEvent::Disconnected:
            // data contains the disconnect reason code
            ESP_LOGW("WiFi", "Disconnected from network, reason: %s", data.c_str());
            break;
        case WifiEvent::ConfigModeEnter:
            ESP_LOGI("WiFi", "Entered config mode");
            break;
        case WifiEvent::ConfigModeExit:
            ESP_LOGI("WiFi", "Exited config mode");
            break;
    }
});

// Check if there are saved Wi-Fi credentials
auto& ssid_list = SsidManager::GetInstance().GetSsidList();
if (ssid_list.empty()) {
    // No credentials saved, start config AP mode
    wifi_manager.StartConfigAp();
} else {
    // Try to connect to the saved Wi-Fi network
    wifi_manager.StartStation();
}
```

Vui lòng kiểm tra https://github.com/78/xiaozhi-esp32 để biết thêm cách sử dụng.
