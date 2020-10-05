https://github.com/node-webrtc/node-webrtc-examples#architecture

# node-webrtc-examples 의 peer signaling 분석

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
Client 의 요청으로 peer 연결 동작이 시작된다.

### Client
#### 1. Server 에 connection 요청하고 id 받음
#### 2. 수신된 peer 에 내 peer 정보를 설정하여 서버에 전달
1. 새로운 peer 생성
1. local peer 에 수신된 remote peer 를 설정
1. application 설정 media 를 `addTrack` 으로 peer 에 설정
1. answer 를 생성
1. 서버에 생성된 peer 의 local description 전달
1. peer close 를 위해 보관

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
      // Server 에 connection 요청하고 id 받음
      const response1 = await fetch(`${host}${prefix}/connections`, {
        method: 'POST'
      });

      const remotePeerConnection = await response1.json();
      const { id } = remotePeerConnection;

      // 새로운 peer 생성
      const localPeerConnection = new RTCPeerConnection({
        sdpSemantics: 'unified-plan'
      });

      ...

      try {
        // local peer 에 수신된 remote peer 를 설정
        await localPeerConnection.setRemoteDescription(remotePeerConnection.localDescription);

        // application 설정 media 를 `addTrack` 으로 peer 에 설정
        await beforeAnswer(localPeerConnection);

        // answer 를 생성
        const originalAnswer = await localPeerConnection.createAnswer();

        // 추가적인 answer 설정
        const updatedAnswer = new RTCSessionDescription({
          type: 'answer',
          sdp: stereo ? enableStereoOpus(originalAnswer.sdp) : originalAnswer.sdp
        });
        await localPeerConnection.setLocalDescription(updatedAnswer);

        // 서버에 생성된 peer 의 local description 전달
        await fetch(`${host}${prefix}/connections/${id}/remote-description`, {
          method: 'POST',
          body: JSON.stringify(localPeerConnection.localDescription),
          headers: {
            'Content-Type': 'application/json'
          }
        });

        // peer close 를 위해 보관
        return localPeerConnection;
        ...
    };
  }
}
```

### Server
#### 1. Post connect
Client 의 peer 연결 요청을 수신한다.
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

// lib > server > connections > webrtcconnection.js
class WebRtcConnection extends Connection {
  constructor(id, options = {}) {
    ...
   // peer 생성
    const peerConnection = new RTCPeerConnection({
      sdpSemantics: 'unified-plan'
    });
    // doOffer 전 option 전처리
    beforeOffer(peerConnection);
    ...
    peerConnection.addEventListener('iceconnectionstatechange', onIceConnectionStateChange);
    const onIceConnectionStateChange = () => { // ice 재연결 callback
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
#### Post remote-description
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
