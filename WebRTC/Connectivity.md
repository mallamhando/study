# WebRTC connectivity
* https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Connectivity

## Signaling
WebRTC 는 중간의 서버 없이는 연결을 생성할수 없다.
이것을 **signal channel** 또는 **signaling service** 라고 부른다.
이러한 종류의 통신 채널은 연결을 설정하기 위한 정보를 서로 교환한다.
교환 방법은 다양하게 선택할수 있는데 이메일, 우편엽서 또는 비둘기로도 가능하다.

교환하려는 정보는 Offer 와 Answer 로 불리며 아래에 기술한 SDP 정보를 가지고 있다.

Peer A 가 연결을 시작하면 Offer 를 생성해야 한다. 그리고 선택한 채널을 통해 Peer B 에 전달한다.
Peer B 는 Offer 를 받은뒤에 Anser 를 생성하고 다시 Peer A 에게 되돌려 준다.

### Session decriptions
WebRTC 연결을 위한 종단 설정을 **session description** 이라고 한다.
Session description 은 보내려고 하는 미디어의 종류에 대한 정보, 형식, 전송 프로토콜, 종단 IP 주소와 port, 그리고 미디어 전송 종단을 서술하기위한 다른 정보들을 포함하고 있다.
이런 정보들은 교환되고 저장되는데 **Session Description Protocol(SDP)** 이라고 불린다.

사용자가 다른 사용자에게 WebRTC call 을 시작할때, **offer** 라고 불리는 특정 description 이 생성된다.
Offer 는 caller 가 제안하는 call 설정에 대한 모든 정보를 가지고 있다.
이것에 대한 응답은 **answer** 라고 부른다. answer 는 반대 종단에 대한 설명이다.
이런 방법으로 각 기기들은 서로에 대한 미디어 데이터 정보를 공유한다.
이런 교환은 **Interactive Connectivity Establishment(ICE)** 를 통해 관리된다.
ICE 는 offer 와 answer 가 분리된 NAT 환경에서도 중간에서 교환할수 있게하는 프로토콜이다.

각 peer 는 자신과 상대의 description 을 저장하는데, 자신의 것을 **local description**, 상대의 것을 **remote description** 이라고 한다.

## ICE candidates
피어는 미디어에 대한 정보를 교환할 뿐만 아니라(위의 제안/답변 및 SDP에서 논의되었듯이) 네트워크 연결에 대한 정보도 교환해야 합니다.
이것을 **ICE candidate** 으로 부르며, 피어가 직접 또는 TURN 서버를 통해 통신할 수 있는 방법을 자세히 설명하는 네트워크 연결 정보를 의미합니다.
일반적으로 각 피어는 가장 좋은 candate 를 먼저 제안하고 이어서 더 나쁜 cadidate 를 전달합니다.
이상적인 candidate 는 UDP 이지만(더 빠르고 미디어 스트림이 중단에서 상대적으로 쉽게 복구할 수 있기 때문에) ICE 표준은 TCP candidate 도 허용합니다.

> 일반적으로 TCP 를 사용하는 ICE candidate 는 UDP 를 사용할 수 없거나 미디어 스트리밍에 적합하지 않은 방식으로 제한되는 경우에만 사용되지만,
> 모든 브라우저가 ICE over TCP 를 지원하는 것은 아닙니다.

ICE 는 candidate 가 TCP 또는 UDP 를 통한 연결을 표현할수 있도록 합니다.
보통 UDP 가 더 일반적으로 선호되고 광범위하게 지원됩니다.
각 프로토콜의 candidate 는 몇 가지의 type 으로 분류되는데, type 에 따라 데이터가 피어에서 피어로 이동하는 방식이 정의됩니다.

### UDP candidate types
**`host`**
**`prflx`**
**`srflx`**
**`relay`**

### TCP candidate types
**`active`**
**`passive`**
**`so`**

### Candidate pair 선택
