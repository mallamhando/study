# ESP SmartConfig
- https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/network/esp_smartconfig.html

> The SmartConfigTM is a provisioning technology developed by TI to connect a new Wi-Fi device to a Wi-Fi network.
> It uses a mobile application to broadcast the network credentials from a smartphone, or a tablet, to an un-provisioned Wi-Fi device.

SmartConfig 는 TI 에서 개발된 provisioning 기술로 새로운 Wi-Fi 기기를 Wi-Fi 네트워크에 연결하게 한다.
이 기술은 모바일 앱이 스마트폰이나 태블릿으로 부터 네트워크 설정값을 브로트캐스트해서 un-provisioned 한 Wi-Fi 장치에 전달한다.

> The advantage of this technology is that the device does not need to directly know SSID or password of an Access Point (AP).
> This information is provided using the smartphone. This is particularly important to headless device and systems, due to their lack of a user interface.

이 기술의 장점은 장치가 직접적으로 AP 의 SSID 나 password 를 알 필요가 없다는 점이다.
정보는 스마트폰에서 사용되기 위해서 제공된다. 이것은 부족한 UI 인터페이스를 가지고 있는 headless 장치나 시스템에게 특히 중요하다

> If you are looking for other options to provision your ESP32 devices, check Provisioning API.

ESP32 장치를 provision 하기 위한 다른 옵션을 알기 위해서는 Provisioning API 를 참조해야 한다.
