# Bluetooth mesh concepts
## Network topology and relaying
### GATT proxy
To enable support for legacy Bluetooth LE devices that do not support receiving mesh packets,
Bluetooth mesh defines a separate protocol for tunneling mesh messages over the Bluetooth LE GATT protocol.
For this purpose, the Bluetooth mesh profile specification defines a GATT bearer and the corresponding GATT Proxy Protocol.
This protocol allows legacy Bluetooth LE devices to participate
in the mesh network by establishing a GATT connection to a mesh device that has the proxy feature enabled.

The legacy device gets assigned an address and the necessary keys to become a full-fledged member of the network.
The device receives the security credentials through the regular provisioning procedure or through some out-of-band mechanism.

메시 패킷 수신을 지원하지 않는 레거시 블루투스 LE 장치(스마트폰?)를 지원하기 위해 블루투스 메시는 블루투스 LE GATT 프로토콜을 통해 메시 메시지를 터널링하기 위한 별도의 프로토콜을 정의한다.
이를 위해 블루투스 메시 프로파일 사양은 GATT 베어러와 해당 GATT 프록시 프로토콜을 정의한다.
이 프로토콜은 프록시 기능이 활성화된 메시 장치에 대한 GATT 연결을 설정하여 레거시 블루투스 LE 장치가 메시 네트워크에 참여할 수 있도록 한다.

레거시 장치에는 네트워크의 완전한 구성원이 되기 위해 필요한 주소와 키가 할당된다.
장치는 정기적인 프로비저닝 절차 또는 일부 대역 외 메커니즘을 통해 보안 자격 증명을 수신한다.
