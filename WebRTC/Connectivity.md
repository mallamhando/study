# WebRTC connectivity
* https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Connectivity

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
