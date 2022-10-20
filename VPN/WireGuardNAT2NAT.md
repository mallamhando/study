# WireGuard NAT To NAT Connections
* https://github.com/pirate/wireguard-docs#nat-to-nat-connections

WG 는 public relay server 없이도 NAT 뒤에 있는 두 client 를 연결할 수도 있지만,
대부분의 경우에서 불가능하다.
NAT-to-NAT 연결은 한 host 가 안정적이고,
공개 IP 주소 + 포트를 사용하고 미리 hardcoding 되어 있으며,
Dynamic DNS 로 업데이트된 FQDN 를 사용하면서,
출력 패킷에 열려있는 NAT 포트가 random 하지 않으면서 공용 IP 와 연결되면서,
모든 피어들의 통신 초기화 과정에서 어떤 설정이든 같은 상태로 유지되어야 한다.


