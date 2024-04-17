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
애플 문서는 백그라운드 모드에서 scan 을 하기위해서는 앱이 허용되어야 한다고 말하고 있다.
하지만 scan 은 반드시 service type 을 명시해야 한다.
wild card scan 을 위해 nil 을 전달하는 것은 백그라운드 모드에서 지원하지 않는다.

```swift
public struct ImpactService {
    public static let service = CBUUID(string: "AFD0FFA0-2A9E-41A9-   B9DB-115A0E511DE4")
}

centralManager.scanForPeripherals(withServices: [ImpactService.service], options: scanOptions)
```

## It is not working. Why not?
애플이 말한대로 하기위새 우리의 BLE peripheral 을 백그라운드 상태에서 scan 하는 기능을 활성화해야 했다.
하지만 장치를 scan 할수가 없었다.
이 상황이 흥미로웠던 것은 Android 에서는 정상적으로 동작했기 때문이다.
iOS 앱 구현이 문제인지 우리의 장치 문제인지 궁금했다.

## nRF Connect 의 advertisement 데이터를 분석하기
Android 에서 bluethooth data 를 보는 최고의 툴 중에 하나는 nRF Connect 이다.
특히 Android 버전은 iOS 것보다 더 기능이 많다.
nRF Connect 에서 scan 한 뒤에 우리는 아래의 화면을 확인 할 수 있었다.

![image](https://github.com/mallamhando/study/assets/30172441/25fd3300-fbdb-4492-a060-0f1e7134177c)

첫번째 아이템은 우리가 찾으려고 했던 그 장치였다.

... 생략

## Active vs Passive scanning
iOS 는 foreground 에서 active scanning 을 하고 background 에서 passive scanning 을 한다.

> activie scanner 는 추가적인 정보를 adveriser 로부터 받기 위해 scan request 를 보낼수 있다.
> 반면, passive scanner 는 단순히 받을수만 있다.
> discovery 와 scanning 이 혼용되어 사용되는 말이라는 것을 알아야 한다.

## Fixing the problem!
Advertising data 수정으로 해결

