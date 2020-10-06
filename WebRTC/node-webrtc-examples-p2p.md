https://github.com/node-webrtc/node-webrtc-examples#architecture

# node-webrtc-examples 의 peer signaling 분석

MDN 의 자료, https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Connectivity 가 더 원론적으로 peer signaling 을 설명하고 있다.
또한 이외에도 peer signaling 을 위한 문서 및 라이브러리 자료들은 많이 있다.
하지만 당장 내 프로젝트와 가장 유사한 예제인 node-webrtc-examples 의 소스를 분석하여 사용한다.

## Init
### Server
Peer 연결 관리 객체를 생성하고 API 와 연결한다. Peer 를 저장하기 위한 Map 객체를 생성하고 메소드를 연결하는 것 이외에 특별한 초기 작업은 없다.

```JS
// index.js
...
function setupExample(example) {
  ...
  const connectionManager = WebRtcConnectionManager.create(options);
  mount(app, connectionManager, `/${example}`);

  return connectionManager;
}
...
const connectionManagers = examples.reduce((connectionManagers, example) => {
  const connectionManager = setupExample(example);
  return connectionManagers.set(example, connectionManager);
}, new Map());

// lib > server > connections > webrtcconnectionmanager.js
WebRtcConnectionManager.create = function create(options) {
  return new WebRtcConnectionManager(...);
};

class WebRtcConnectionManager {
  constructor(options = {}) { 
    ...
    const connectionManager = new ConnectionManager(options);
    ...
}

// lib > server > connections > connectionmanager.js
class ConnectionManager {
  constructor(options = {}) {
    ...
    this.createConnection = () => { ...
    this.getConnection = id => { ...
    this.getConnections = () => { ...
}

// lib > server > rest > connectionsapi.js
function mount(app, connectionManager, prefix = '') {
  app.get(`${prefix}/connections`, (req, res) => { ...
  app.post(`${prefix}/connections`, async (req, res) => { ...
  app.delete(`${prefix}/connections/:id`, (req, res) => { ...
  app.get(`${prefix}/connections/:id`, (req, res) => { ...
  app.get(`${prefix}/connections/:id/local-description`, (req, res) => { ...
  app.get(`${prefix}/connections/:id/remote-description`, (req, res) => { ...
  app.post(`${prefix}/connections/:id/remote-description`, async (req, res) => { ...
}
```

### Client
Server 와 마찬가지로 peer 연결 메소드가 있는 객체를 선언하는 것 이외에 특별한 동작이 없다.

```JS
// examples > broadcaster > client.js
createExample('broadcaster', description, { beforeAnswer });

// lib > browser > example.js
function createExample(name, description, options) {
  ...
  const connectionClient = new ConnectionClient();
  ...
}

// lib > client > index.js
class ConnectionClient {
  constructor(options = {}) {
    options = {
      RTCPeerConnection: DefaultRTCPeerConnection,
      ...
    };
    ...
    this.createConnection = async (options = {}) => {
    ...
```

## Start
### 1. \[Client] server peer 요청
Client 가 Server 에 peer 연결을 요청한다.

```JS
// lib > browser > example.js
function createExample(name, description, options) {
  ...
  createStartStopButton(async () => {
    peerConnection = await connectionClient.createConnection(options);
    window.peerConnection = peerConnection;
  ...
}

// lib > client > index.js
class ConnectionClient {
    ...
    this.createConnection = async (options = {}) => {
      ...
      const response1 = await fetch(`${host}${prefix}/connections`, {
        method: 'POST'
      });

      const remotePeerConnection = await response1.json();
      const { id } = remotePeerConnection;
    ...
```

### 2. \[Server] peer 연결 요청 수신
Server 가 client 의 peer 연결 요청을 수신하고 peer 생성 동작을 시작한다.

```JS
// lib > server > rest > connectionsapi.js
function mount(app, connectionManager, prefix = '') {
  ...
  app.post(`${prefix}/connections`, async (req, res) => {
    try {
      const connection = await connectionManager.createConnection();
      ...
  });
  ...

// lib > server > connections > webrtcconnectionmanager.js
class WebRtcConnectionManager {
    ...
    this.createConnection = async () => {
      const connection = connectionManager.createConnection();
      ...
    ...
}
```

### 3. \[Server] peer 생성 준비 작업
Peer id 를 위한 UUID 를 만들고 close listener 를 등록한다.
Server 전체의 peer 를 관리하는 Map 객체에 id 에 맞는 peer 를 등록 시킨다.

```JS
// lib > server > connections > connectionmanager.js
class ConnectionManager {
    ...
    this.createConnection = () => {
      const id = createId(); // uuid 생성
      const connection = new Connection(id); // WebRtcConnection 생성

      // 1. Add the "closed" listener.
      ...
      // 2. Add the Connection to the Map.
      ...
}
```

### 4. \[Server] peer 생성
```JS
// lib > server > connections > webrtcconnection.js
class WebRtcConnection extends Connection {
  constructor(id, options = {}) {
    ...
    const peerConnection = new RTCPeerConnection({
      sdpSemantics: 'unified-plan'
    });
```

### 5. \[Server] 비지니스 로직을 peer 에 등록
미디어 또는 데이터 전송을 위한 비지니스 로직을 peer 에 등록시킨다.

> 비지니스 로직의 실행 시작 시점이다.
> 하지만 비지니스 로직의 데이터 전달 시점은 아니다.
> 데이터 전달은 peer 연결이 완료될 때 시작된다.
> 비지니스 로직의 실행 시작 시점과 비지니스 로직 데이터의 전달 시점은 다르다.

```JS
// lib > server > connections > webrtcconnection.js
    beforeOffer(peerConnection);
```

### 6. \[Server] ice 연결
ICE server 에 연결한다. 연결이 안될경우 다시 시도할수 있는 재연결 로직이 있다.

```JS
// lib > server > connections > webrtcconnection.js
    peerConnection.addEventListener('iceconnectionstatechange', onIceConnectionStateChange);
    const onIceConnectionStateChange = () => { // ice 재연결 callback

// lib > server > connections > webrtcconnectionmanager.js
class WebRtcConnectionManager {
    ...
    this.createConnection = async () => {
      ...
      await connection.doOffer();
    ...
}

// lib > server > connections > webrtcconnection.js
class WebRtcConnection extends Connection {
  constructor(id, options = {}) {
    ...
    this.doOffer = async () => {
      const offer = await peerConnection.createOffer();
      await peerConnection.setLocalDescription(offer);
      try {
        await waitUntilIceGatheringStateComplete(peerConnection, options);
    ...
}
```

### 7. \[Server] peer 전달을  위한 description
Client 에 peer 를 전달하기 위한 peer description 을 생성한다. toJSON 프로토타입을 사용한다.

```JS
// lib > server > connections > webrtcconnection.js
    ...
    Object.defineProperties(this, {
      iceConnectionState: // peerConnection.iceConnectionState;
      ...
      localDescription: // descriptionToJSON(peerConnection.localDescription, true);
      ...
      remoteDescription: // descriptionToJSON(peerConnection.remoteDescription);
      ...
      signalingState: // peerConnection.signalingState;
      ...
}

// lib > server > connections > webrtcconnection.js
class WebRtcConnection extends Connection {
  constructor(id, options = {}) {
    ...
    this.toJSON = () => {
      return {
        ...super.toJSON(),
        iceConnectionState: this.iceConnectionState,
        localDescription: this.localDescription,
        remoteDescription: this.remoteDescription,
        signalingState: this.signalingState
      };
    };
    ...
}
```

### 8. \[Server] peer description 을 client 에 전달
```JS
// lib > server > rest > connectionsapi.js
function mount(app, connectionManager, prefix = '') {
  ...
  app.post(`${prefix}/connections`, async (req, res) => {
    try {
      const connection = await connectionManager.createConnection();
      res.send(connection);
      ...
  });
  ...
```

### 9. \[Client] peer 수신
```JS
// lib > client > index.js
      const remotePeerConnection = await response1.json();
      const { id } = remotePeerConnection;
```

### 10. \[Client] 새로운 local peer 생성
```JS
// lib > client > index.js
      const localPeerConnection = new RTCPeerConnection({
        sdpSemantics: 'unified-plan'
      });
```

### 11. \[Client] remote peer 등록
local peer 의 remote peer 로 수신된 peer 를 등록한다.

```JS
// lib > client > index.js
        await localPeerConnection.setRemoteDescription(remotePeerConnection.localDescription);
```

### 11. \[Client] 비지니스 로직을 등록
미디어, 파일 전송 데이터 등을 위한 비지니스 로직을 Client peer 에 등록한다.

> Client 의 비지니스 로직은 이때 시작한다.

```JS
// lib > client > index.js
        await beforeAnswer(localPeerConnection);
```

### 12. \[Client] 추가 정보 등록
음향 설정 등의  비지니스 로직의 추가적인 정보가 필요할 경우 local peer 의 answer 객체를 생성해서 추가 정보를 등록한다.

```JS
// lib > client > index.js
        const originalAnswer = await localPeerConnection.createAnswer();

        const updatedAnswer = new RTCSessionDescription({
          type: 'answer',
          sdp: stereo ? enableStereoOpus(originalAnswer.sdp) : originalAnswer.sdp
        });
        await localPeerConnection.setLocalDescription(updatedAnswer);
```

### 13. \[Client] 서버에 local peer description 전송
```JS
// lib > client > index.js
        await fetch(`${host}${prefix}/connections/${id}/remote-description`, {
          method: 'POST',
          body: JSON.stringify(localPeerConnection.localDescription),
          headers: {
            'Content-Type': 'application/json'
          }
        });
```

### 14. \[Server] Client 의 peer 정보를 수신
Client 의 peer 를 수신하고 server peer 에 remote peer 로 등록한다.
상호간의 등록이 완료되면 peer 의 비지니스 로직이 전송이 시작된다.

```JS
// lib > server > rest > connectionsapi.js
function mount(app, connectionManager, prefix = '') {
  ...
  app.post(`${prefix}/connections/:id/remote-description`, async (req, res) => {
    const { id } = req.params;
    const connection = connectionManager.getConnection(id);
  ...
    try {
      await connection.applyAnswer(req.body);
      res.send(connection.toJSON().remoteDescription);
  ...

// lib > server > connections > webrtcconnection.js
class WebRtcConnection extends Connection {
  constructor(id, options = {}) {
    ...
    this.applyAnswer = async answer => {
      await peerConnection.setRemoteDescription(answer);
    };
    ...
}
```

### 15. \[Client] peer close 를 위해 보관
```JS
// lib > client > index.js
class ConnectionClient {
    ...
    this.createConnection = async (options = {}) => {
      ...
        return localPeerConnection;
        ...
    };
  }
}
```

## Stop
### Server
```JS
// lib > server > connections > webrtcconnection.js
class WebRtcConnection extends Connection {
  constructor(id, options = {}) {
    ...
    this.close = () => {
      ...
      peerConnection.close();
      super.close();
    };
    ...
}
```

### Client
```JS
// lib > browser > example.js
function createExample(name, description, options) {
  ...
  createStartStopButton(async () => {
    ...
  }, () => {
    peerConnection.close();
  });
  ...
}

// lib > client > index.js
class ConnectionClient {
    ...
    this.createConnection = async (options = {}) => {
      ...
      // peer 를 close 하기 전에 server 에 delete 요청
      localPeerConnection.close = function() {
        fetch(`${host}${prefix}/connections/${id}`, { method: 'delete' }).catch(() => {});
        return RTCPeerConnection.prototype.close.apply(this, arguments);
      };
      ...
  }
}
```
