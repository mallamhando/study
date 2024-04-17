# iOS — Scan and Connect to a BLE peripheral in the background
- https://medium.com/@cbartel/ios-scan-and-connect-to-a-ble-peripheral-in-the-background-731f960d520d

## Connecting to a device while the app is in the background
애플의 공식 문서에서 BLE 장치를 백그라운드 상태에서 발견하고 연결하기 위해서는 올바른 백그라운드 모드 설정과 원하는 service 의 UUID 를 설정해야 한다고 되어 있다.

## 올바른 백그라운드 모드 설정
“Uses Bluetooth LE accessories” 백그라운드 모드가 Xcode 에서 활성화 되어야 한다.

> 애플은 이렇게 언급한다.
> 앱이 백그라운드에 있을때도 장치들을 발견하고 연결할수 있습니다.
> 게다가 시스템은 CBCentralManagerDelegate, CBPeripheralDelegate 에서 delegate 메소드를 실행할때 앱을 깨울수도 있습니다.
> 앱은 연결 완료, 끊김 등의 중요한 centrol role 이벤트를 handle 할수 있고,
> 장치들은 characteristic 의 업데이트를 전송하거나는 centrol manager 의 상태가 바뀔때도 앱은 실행될수 있습니다.

## Service UUID SCAN
