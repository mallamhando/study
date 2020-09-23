# WebRTC
https://developer.mozilla.org/ko/docs/Web/API/WebRTC_API

## Diary
### 2020-09-23
어려운 점은 설명이 너무 장황하다는 것이다.
문서의 길이가 너무 길고 읽어보면 기초적인것을 설명하고 있다.
내가 원하는 부분을 딱 맞게 찾기가 어렵다.

최대한 타겟팅해서 읽으려고 노력해야 한다.
잘모르고 시작한다는 것을 전재로 천천히 진행해야 한다.

가장 중요한 것은 스스로 확신을 갖는것이다.
이게 정말 중요하고 필요하다는 것을 스스로 인지해야 한다.

WebRTC 가 될경우 내가 원하는 Serverless IoT 를 좀 더 쉽게 구현할수 있게 된다.

## 참고자료
* https://github.com/node-webrtc/node-webrtc
* https://github.com/medooze/media-server-node

## 용어 설명
### ICE
브라우저가 peer 연결을 위해 사용하는 framework, ICE 는 STUN 또는 TURN server 를 사용한다.

### STUN
Peer 가 NAT 를 통해서 실제로 어떤 Public IP 와 port 를 사용하고 있는지를 알려주는 Server,
Peer 들끼리 공유해서 통신을 할 수 있게 한다.

### TURN
새로운 Peer 에 대한 연결을 거부하는 NAT 를 사용할 경우에는 TURN Server 를 이용해야 한다.
TURN 서버는 Peer 사이의 데이터를 직접 전달해준다.

### SDP
전송 데이터의 내용이 아닌 전송 데이터에 대한 정보 
