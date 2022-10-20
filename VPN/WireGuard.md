# WireGuard


### Simple & Easy-to-use
SSH 로 설정하고 배포하기 쉽게하는 것이 목적입니다.
VPN 연결이 단순히 public key(정확히 말하면 SSH key) 를 주고받는것만으로 시작되며,
이후에는 WG 로 투명하게 관리된다.
[Mosh](http://mosh.mit.edu/) 처럼 IP address 들의 이동에도 적합하며,
연결을 관리해야할 필요도, 상태를 관리할 필요도, 내부를 깊게 살펴볼 필요도 없다.
WG 는 극단적으로 간단하면서도 강력한 인터페이스를 구현한다.

### Cryptographically Sound
WG 는 최신의 암호화 기술을 사용한다.
Noise protocol framework, Curve25519, ChaCha20, Poly1305, BLAKE2, SipHash24, HKDF 및 보안 구조를 가지고 있다.
이것들은 보수적이고 합리적이며 보안 관계들에게서 인증을 받았다.

### Minimal Attack Surface
WG 는 쉬운 구현과 단순함을 기준으로 설계되었다. 이것은 적은 수의 코드로 쉽게 구현되었다는 것을 의미한다.
또한 보안적 취약점이 쉽게 감지된다는 것을 의미한다.
IPSec 또는 OpenVPN 같은 대기업들이 사용하는 거대한 크기의 코드를 검증하는 일은
너무 크기 때문에 심지어 큰 보안 조직에게도 어려운 일이다.
WG 는 한 개인에게도 철저하게 검증될 수 있다.

### High Performance
극단적으로 높은 속도의 암호화 프로그램 조합과 WG 가 linux kernel 내부에 살아있다는 사실은 보안 networking 이 매우 빠른 속도로 동작된다는 것을 의미한다. 스마트폰 같이 작은 embedded 기기 뿐만 아니라 backbone router 모두에도 적합하다.

### Well Defined & Thoroughly Considered
WG 는 지루하고 철저하게 고려된 학문적 과정 및 기술 백서의 결과물이다.
기술 백서는 프로토콜을 명확하게 정의하고 각 결정에 도달하기 위한 상세한 과정이 나와있는 학문적인 연구 논문이다.

## Links
* https://ziwon.github.io/post/wireguard/
* https://www.wireguard.com/
* https://github.com/pirate/wireguard-docs

## Conceptual Overview

기본 컨셉이 궁금하면 계속 읽고, 사용법만 궁금하다면 Quick start 와 설치 방법 링크를 참조하도록 한다.

만약 내부 동작과 프로토콜의 짧은 요약이 궁금하다면 기술 백서를 자세하게 읽어야 한다.

만약 WG 를 새로운 platform 에 구현하기 원한다면 cross-platform note 를 참조해야 한다.

WG 는 보안적으로 캡슐화된 데이터를 UDP 로 전송한다.
WG interface 를 더하면, private key 와 peer 의 public key 를 설정한뒤에는 데이터 packet 을 주고 받을 수 있게 된다.
key 를 주고 받는 방법은 WG 의 범위가 아니다.
key 주고 받는 기능은 IKE 나 OpenVPN 처럼 프로그램을 커지게 만든다.
상대적으로 SSH 와 Mosh 의 유사본에 가깝다. SSH 와 Mosh 는 상대의 public key 를 주고받는 방식을 사용하면서 통신을 위한 packet 교환 interface 를 구성한다.

### Simple Network Interface

WG 는 eth0 또는 wlan0 같은, wg0 (또는 wg1, wg2, wg3, 등) 라고 불리는 network interface 를 하나 또는 여려개를 더하는 것으로 작동한다.

이 network interface 는 더해진 뒤에 ifconfig 도는 ip-address 같은 도구로 설정될 수 있다.
route 또는 ip-route 로 route 설정이 더해지거나 제거될수 있으며,
또한 이외의 모든 network 툴로도 사용이 가능한다.

WG interface 의 특수한 기능은 wg 툴로 설정될수 있다. 이 interface 는 tunnel interface 로 동작한다.

WG 는 tunnel IP 주소를 public key 와 원격 endpoint 로 구성할수 있다.
interface 가 peer 로 패킷을 보내면 다음과 같은 과정이 실행된다.

1. 이 패킷은 192.168.30.8 로 전송된다. 그럼 어떤 peer 거지? 한번 볼까...좋아 이건 ABCDEFGH peer 것이군.(못찾는다면 버림)
2. IP 패킷 전체를 ABCDEFGH 의 public key 로 암호화 한다.
3. ABCDEFGH 의 원격 endpoint 가 무엇이지? 어디 한번 볼까...좋아, endpoit 는 216.58.211.110 의 53133 UDP 포트야.
4. 2 번에서 암호화된 패킷을 UDP 로 216.58.211.110:53133 에 전송한다. 끗

패킷을 수신하는 쪽에서는 다음과 같은 일이 발생한다.

1. 나 방금 98.139.183.24 의 7361 UDP 포트에서 패킷을 받았어, 그걸 해독해보자!
2. 이 패킷은 LMNOPQRS 으로부터 적절하게 인증되고 암호화되어 전송되었어, 좋아 peer LMNOPQRS 의 most recent Internet endpoint 를 UDP 를 이용한 98.139.183.24:7361 이라고 기억해 두자.
3. 한번 해독된 plain-text 패킷이 192.168.43.89 에서 온것이야. LMNOPQRS peer 는 192.168.43.89 처럼 우리에게 패킷을 보내는것이 허용되었어?
4. 만약 그렇다면, 패킷의 interface 접속을 허용할께, 아니라면 버리고!

이런 동작의 뒤에는 적절한 정책들과 인증, 최신의 암호화 기술이 적용된 완벽한 단방향 보안이 적용되어 있다.
