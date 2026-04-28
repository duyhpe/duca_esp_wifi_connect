message("duyhpe/esp_wifi_connect: IDF_VER is ${IDF_VER}")

if(${IDF_VER} STRGREATER_EQUAL "v6.0")
    # v6
    message("duyphe/esp_wifi_connect: sử dụng idf_component_register phiên bản 6.x")

    idf_component_register(
        SRCS
            "wifi_manager.cc"
            "wifi_configuration_ap.cc"
            "wifi_station.cc"
            "ssid_manager.cc"
            "dns_server.cc"
        INCLUDE_DIRS
            "include"
        EMBED_TXTFILES
            "assets/wifi_configuration.html"
            "assets/wifi_configuration_done.html"
        REQUIRES "esp_timer" "esp_http_server" "esp_wifi" "nvs_flash"
    )
else()
    # v5
    message("duyhpe/esp_wifi_connect: sử dụng idf_component_register phiên bản 5.x")

    idf_component_register(
        SRCS
            "wifi_manager.cc"
            "wifi_configuration_ap.cc"
            "wifi_station.cc"
            "ssid_manager.cc"
            "dns_server.cc"
        INCLUDE_DIRS
            "include"
        EMBED_TXTFILES
            "assets/wifi_configuration.html"
            "assets/wifi_configuration_done.html"
        REQUIRES "esp_timer" "esp_http_server" "esp_wifi" "nvs_flash" "json"
    )
endif()
