# WireGuard
* https://www.wireguard.com/

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

WireGuard works by adding a network interface (or multiple),
like eth0 or wlan0, called wg0 (or wg1, wg2, wg3, etc).
This network interface can then be configured normally using ifconfig(8) or ip-address(8),
with routes for it added and removed using route(8) or ip-route(8),
and so on with all the ordinary networking utilities.
The specific WireGuard aspects of the interface are configured using the wg(8) tool. This interface acts as a tunnel interface.

WireGuard associates tunnel IP addresses with public keys and remote endpoints. When the interface sends a packet to a peer, it does the following:

This packet is meant for 192.168.30.8. Which peer is that? Let me look...
Okay, it's for peer ABCDEFGH. (Or if it's not for any configured peer, drop the packet.)
Encrypt entire IP packet using peer ABCDEFGH's public key.
What is the remote endpoint of peer ABCDEFGH? Let me look... Okay, the endpoint is UDP port 53133 on host 216.58.211.110.
Send encrypted bytes from step 2 over the Internet to 216.58.211.110:53133 using UDP.
When the interface receives a packet, this happens:

I just got a packet from UDP port 7361 on host 98.139.183.24. Let's decrypt it!
It decrypted and authenticated properly for peer LMNOPQRS.
Okay, let's remember that peer LMNOPQRS's most recent Internet endpoint is 98.139.183.24:7361 using UDP.
Once decrypted, the plain-text packet is from 192.168.43.89. Is peer LMNOPQRS allowed to be sending us packets as 192.168.43.89?
If so, accept the packet on the interface. If not, drop it.
Behind the scenes there is much happening to provide proper privacy, authenticity, and perfect forward secrecy, using state-of-the-art cryptography.
