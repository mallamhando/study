# WireGuard Endpoint Discovery and NAT Traversal using DNS-SD
* https://www.jordanwhited.com/posts/wireguard-endpoint-discovery-nat-traversal/

## WireGuard
WG 는 Jason A. Donenfeld 가 만들어낸 다음 세대의 cross-platform VPN 기술로,
빠르게 beefy, IPSec, SSL VPN solution 들을 대체해 왔다.
여러 성공적인 테스트를 거쳐 Linux v5.6 에도 기본적으로 merge 되었다.
Kernel module 또는 Go/Rust 로 작성된 user application 으로도 사용이 가능하다.
WG 에 처음이라면 https://www.wireguard.com/#conceptual-overview 를 먼저 읽어보는 것이 편하다.

## Dynamically Addressed Peers