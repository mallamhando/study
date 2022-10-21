# WireGuard NAT To NAT Connections
* https://github.com/pirate/wireguard-docs#nat-to-nat-connections

WG 는 public relay server 없이도 NAT 뒤에 있는 두 client 를 연결할 수도 있지만,
대부분의 경우에서 불가능하다.
NAT-to-NAT 연결은 한 host 가 안정적이고,
공개 IP 주소 + 포트를 사용하고 미리 hardcoding 되어 있으며,
Dynamic DNS 로 업데이트된 FQDN 를 사용하면서,
출력 패킷에 열려있는 NAT 포트가 random 하지 않으면서 공용 IP 와 연결되면서,
모든 피어들의 통신 초기화 과정에서 어떤 설정이든 같은 상태로 유지되어야 한다.

알려진 ip 와 port 가 먼저 설정되어야 한다.
WG 는 공공 STUN 서버를 이용한 동적으로 다른 host 를 찾는 작업을 할수가 었다.
WG 는 signaling layer 가 없기 때문이다.
WebRTC 는 두개의 NAT 사이에서 동적으로 연결 설정을 할수 있는 프로토콜의 예이다.
하지만, 각 host 사이의 IP:port 조합을 얻기위해 대역 외의 signaling 서버를 사용하는 문제가 있다.
WG 는 이런것을 가지고 있지 않다.
따라서 hardcoding 된 Endpoint + ListenPort 조합을 사용해야 한다.

https://tailscale.com/blog/how-nat-traversal-works/ 에서 더 많은 내용을 알수 있다.

## Requirements for NAT-to-NAT setups
* 적어도 하나의 peer 는 직접 연결되는 `Endpoint` 에 hardcoding 되어야 한다. 만약 두 Peer 모두 지속적인 IP 가 없이 NAT 뒤에 있다면, Dynamic DNS 나 다른 지속적인 solution 이 필요하다. 공용적으로 접근 하는한 domain/IP 가 적어도 한 peer 에 필요하다.
* 적어도 하나의 peer 는 hardcoded UDP `ListenPort` 가 필요하다. NAT router 는 반드시 UDP source port 를 randomization 하면 안된다. 반대로 수신 packet 은 hardcoding 된 `ListenPort` 로 보내지고 NAT 가 출력 packet 을 위해 정의한 random port 를 사용하는 대신 버려진다.
* 모든 NAT 안의 peer 들은 반드시 PersistentKeepalive 옵션을 활성화해야 한다. 그렇게 NAT 의 routing table 안에서 유지해야 한다.

## The hole-punching connection process
1. Peer1 가 Peer2 에게 UDP packet 을 보낸다. Peer2 의 NAT 가 즉시 거절한다. 하지만 괜찮다. 이것은 Peer1 의 NAT 가 어떤 UDP 응답을 돌려받는지 확인하기 위한 과정이다.
1. Peer2 가 Peer1 에게 UDP pacekt 을 보낸다. 이것은 Peer1 NAT 에게 기대되었던 응답이기 때문에 인정되고 전달된다. Peer1 NAT 에게는 Peer2 에 보낸 데이터의 응답이기 때문이다. 따라서 Peer1 은 Peer2 의 UDP packet 을 받는다.
1. Peer1 이 Peer2 의 UDP 응답 Packet 을 전송한다. Peer2 의 NAT 는 자신의 출력 패킷에 대한 응답이기 때문에 받아들이고 Peer2 에게 전달한다.

첫번째 packet 이 거절되지만 그 뒤 응답에 대한 packet 부터는 받아들여지는 과정을 'UDP hole-puching' 이라고 부른다.


