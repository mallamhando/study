# Core Bluetooth Background Processing for iOS Apps
- https://developer.apple.com/library/archive/documentation/NetworkingInternetWeb/Conceptual/CoreBluetooth_concepts/CoreBluetoothBackgroundProcessingForIOSApps/PerformingTasksWhileYourAppIsInTheBackground.html#//apple_ref/doc/uid/TP40013257-CH7-SW1

iOS 앱에게 있어 앱이 foreground 에 있는지 background 에 있는지는 중요한 일이다.
앱은 반드시 background 에 있을때와 foreground 에 있을때가 다르게 동작해야 한다.
왜냐하면 시스템 리소스가 제한되기 때문이다.
우선 iOS 의 background 동작을 이해해야 한다.  Background Execution in App Programming Guide for iOS.

- https://developer.apple.com/library/archive/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html#//apple_ref/doc/uid/TP40007072-CH4
- https://developer.apple.com/library/archive/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007072

기본적으로, 일반적인 많은 Core Bluetooth task 들 - central, peripheral 단의 - 은 백그라운드 모드나 suspend 상태에서 비활성화된다.
즉, 앱이 관련된 이벤트가 있을때 suspend 상태에서 깨어나기 위해서는 앱을 반드시 Core Bluetooth Background execution mode 로 설정해야 한다는 뜻이다.
앱이 background 동작의 모든 기능을 필요로 하지 않는다 해도, 중요한 이벤트가 발생할때 시스템에 의해 alert 을 받을수 있다.

앱이 Core Bluetooth Background execution mode 중 하나라도 지원할지라도,
계속 동작되는 것은 아니다.
어떤 시점에는 시스셈이 foreground app 을 위한 충분한 메모리 공간을 확보하기 위해 해당 앱을 강제로 종료 시키면서 연결되어 있는 상태도 끊어 버릴수 있다.
iOS7 처럼 Core Bluetooth 는 central 과 peripheral manager object 를 위한 저장된 상태 정보를 지원한다.
그리고 실행될때 그것들을 복원해서 사용한다.
이러한 기능들을 이용해 Bluetooth 장치를 이용한 긴시간의 동작을 지원할 수 있다.

## Foreground-Only Apps

대부분의 iOS 앱들처럼, 특정 background task 를 수행하기 위한 권한을 요청하지 않으면,
앱은 background 모드에서 곧 suspened 상태로 바뀌게 된다.
suspend 상태에서는 Bluetooth 관련 task 들을 수행할수 없을뿐만 아니라 Bluetooth 와 관련된 어떤 이벤트도 수신할수 없다.

Central 모드일때, foreground-only app-Core Bluetooth Background execution mode 지원이 선언되지않은-들은 background 또는 suspend 상태에서 다른 advertising peripheral 을 scan 하고 discover 할수 없다.
Peripheral 모드일때, advertising 은 비활성화 된다. 그리고 어떤 central 이 요청하는 characteristic 값에 대한 접근 시도는 에러를 수신하게 된다.

사용 사례에 따라 이러한 기본 동작은 앱에게 여러가지 방법을 주의하게 한다.
예를 들어, 현재 연결된 Bluetooth 장치와 데이터를 주고 받고 있다고 생각할때,
사용자가 앱을 suspend 상태로 보내게 된다면,
suspend 상태의 앱은 foreground 상태로 이동하기 전까지는 연결이 끊어졌다는 이벤트를 수신할수 없다.

### Take Advantage of Peripheral Connection Options
Foreground 상태에서 발생하는 모든 Bluetooth 관련 이벤트들은 suspend 상태에서 queue 에 저장되었다가 앱이 foreground 상태가 되었을떄 전달된다.
Core Bluetooth 는 어떤 종류의 central role 이벤트들이 발생했는지 사용자에게 alert 하는 방법이 있다.
사용자들이 이러한 alert 들을 이용해서 앱이 foreground 로 돌아와야 할지를 결정할수 있다.

이러한 이어지는 peripheral 연결 옵션을 이용해 이러한 alert 기능을 사용할수 있다.
`CBCentralManager` class 의 `connectionPeripheral:options:` 메소드를 사용하는 것이 그렇다.

- `CBConnectPeripheralOptionNotifyOnConnectionKey`-만약 시스템이 앱이 suspend 상태에 있을 동안 peripheral 과 성공적으로 연결이 되었을때 alert 을 보여주기 원한다면 이 키를 포함해야 한다.
- `CBConnectPeripheralOptionNotifyOnDisconnectionKey`-만약 시스템이 앱이 suspend 상태에 있을 동안 peripheral 과 연결이 끊겼을때 alert 을 보여주기 원한다면 이 키를 포함해야 한다.
- `CBConnectPeripheralOptionNotifyOnNotificationKey`-만약 시스템이 앱이 suspend 상태에 있을 동안 peripheral 으로 받는 모든 notification 에 대한 alert 을 보여주기 원한다면 이 키를 포함해야 한다.

자세한 내용은 https://developer.apple.com/documentation/corebluetooth/cbcentralmanager 를 참조한다.

## Core Bluetooth Background Execution Modes

### The bluetooth - central Background Execution Mode

### The bluetooth - peripheral Background Execution Mode


## Use Background Execution Modes Wisely


## Performing Long-Term Actions in the Background

### State Preservation and Restoration

### Adding Support for State Preservation and Restoration

#### Opt In to State Preservation and Restoration

#### Reinstantiate Your Central and Peripheral Managerss

#### Implement the Appropriate Restoration Delegate Method

#### Update Your Initialization Process
