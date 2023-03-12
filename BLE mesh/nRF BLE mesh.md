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

### Addressing
The Bluetooth mesh addressing scheme is different from the Bluetooth LE addressing scheme. It features three types of addresses:

* Unicast addresses - Unique for every device
* Group addresses - For forming a group of devices and addressing them all at once
* Virtual addresses - Untracked UUID-based addresses with a large address space

When a device is added to a network, it is assigned a range of unicast addresses that represents it. A device’s unicast addresses cannot be changed and are always sequential. The unicast address space supports having 32767 unicast addresses in a single mesh network. Unicast addresses can be used by any application to directly send a message to a device.

Group addresses are allocated and assigned as part of the network configuration procedure. A group address may represent any number of devices, and a device may be part of any number of groups. There can at most be 16127 general purpose group addresses in a mesh network.

Virtual addresses can be considered a special form of group addresses, and can be used to represent any number of devices. Each virtual address is a 128-bit UUID generated from a text label. The virtual addresses do not have to be tracked by a network configuration device, and in this way, users can generate virtual addresses before deployment or addresses can be generated ad-hoc between devices in the network.

네트워크에 있는 모든 device 는 별도의 unicast 주소를 갖는다. 하지만 항상 0 부터 32767 의 순서대로 부여되어야 한다.
(Unicast 주소는 네트워크가 구성(provisioning?) 될때 생기는것일까? 아니면 한번 설정되면 항상 같은 값이어야 하는 것일까?)

그룹 주소는 네트워크 설정 과정의 한 부분을 담당하고 있다. 그룹 주소는 여러 device 들의 모음을 대표한다. 한 메시 네트워크에는 16127 개의 그룹이 생성될 수 있다.

가상 주소는 그룹 주소의 특별한 형태이다. 마찬가지로 여러 device 들의 모음을 대표한다.
가상 주소는 text label 로 생성된 128-bit 의 UUID 이다. 가상 주소는 네트워크 설정 기기에 의해 추적될 필요는 없다.
따라서 사용자는 배포 이전 또는 임시로 가상 주소를 생성할 수 있다.
