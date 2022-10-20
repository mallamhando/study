# WireGuard Routing & Network Namespace Integration
* https://www.wireguard.com/netns/

Linux network interface 처럼, WG 도 network namespace 환경을 포함하고 있다.
관리지가 완전히 다른 여러 네트워크 subsystem 을 가지고, 선택할 수 있다는것을 의미한다.

WG 는 신기한것을 한다. WG interface 가 생성될때, namespace 를 기억한다.
("난 namespace A 에서 생성되었어.")
나중에 WG 는 새로운 namespace 로 바뀔수 있지만("난 B 라는 이름으로 바뀌고 있어."), 첫번째 이름(A)를 기억한다.

WG 는 UDP 소켓을 실제 암호화된 packet 을 주고받는데 사용한다.
이 socket 들은 항상 namespace A - 처음 생성될때 얻은 - 안에서 살고 있다.
이것은 아주 멋진 속성을 가져다 준다.
즉, WG interface 를 A 라는 namespace 로 생성하고, B namespace 로 이동할 수 있으며,
A namespace 에서 받은 암호화된 UDP packet 은 B namespace 에 해독된 packet 형태로 전달받을 수 있다.

(같은 기술이 userspace TUN 기반 interface 에도 유효하다는 것을 알아야 한다...)

이것은 어떤 멋진 가능성을 열어준다.

## Ordinary Containerization
