# WebRTC
https://developer.mozilla.org/ko/docs/Web/API/WebRTC_API

## 참고자료
* https://github.com/node-webrtc/node-webrtc
* https://github.com/medooze/media-server-node
* https://webrtc.github.io/samples/

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
