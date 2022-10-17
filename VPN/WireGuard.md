# WireGuard
* https://www.wireguard.com/

## Simple & Easy-to-use
SSH 로 설정하고 배포하기 쉽게하는 것이 목적입니다.
VPN 연결이 단순히 public key(정확히 말하면 SSH key) 를 주고받는것만으로 시작되며,
이후에는 WG 로 투명하게 관리된다.
[Mosh](http://mosh.mit.edu/) 처럼 IP address 들의 이동에도 적합하며,
연결을 관리해야할 필요도, 상태를 관리할 필요도, 내부를 깊게 살펴볼 필요도 없다.
WG 는 극단적으로 간단하면서도 강력한 인터페이스를 구현한다.

## Cryptographically Sound
WG 는 최신의 암호화 기술을 사용한다.
Noise protocol framework, Curve25519, ChaCha20, Poly1305, BLAKE2, SipHash24, HKDF 및 보안 구조를 가지고 있다.
이것들은 보수적이고 합리적이며 보안 관계들에게서 인증을 받았다.

## Minimal Attack Surface


## Simple Network Interface
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
