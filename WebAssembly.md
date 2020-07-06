### F/W 개발자에게 필요한 WebAssembly
웹앱과 펌웨어 소스를 공유할 수 있어 중복 코딩을 줄일수 있다.
특히 그래픽과 관련된 c 라이브러리를 쉽게 웹앱과 펌웨어 또는 PC 에서 동시에 사용할 수 있게 된다.

고객에게 시뮬레이션 기능을 제공하여 도메인 코드의 빠른 검증이 가능해진다.

프로토타입 언어인 JS 를 이용하여 목업과 테스트 코드를 빠르게 만들수 있어 테스트 주도 개발이 쉬어진다.

Rust, Typescript, JS 와 같은 언어로 새로운 기능을 시뮬레이션 구현하고 WebAssembly 로 변환한 뒤에 실제 코드에서 라이브러리로 활용할수 있다.

### 기타
* [언제 웹어셈블리를 사용하는 게 좋은가](https://engineering.huiseoul.com/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%9E%91%EB%8F%99%ED%95%98%EB%8A%94%EA%B0%80-%EC%9B%B9%EC%96%B4%EC%85%88%EB%B8%94%EB%A6%AC%EC%99%80%EC%9D%98-%EB%B9%84%EA%B5%90-%EC%96%B8%EC%A0%9C-%EC%9B%B9%EC%96%B4%EC%85%88%EB%B8%94%EB%A6%AC%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EA%B2%8C-%EC%A2%8B%EC%9D%80%EA%B0%80-cf48a576ca3)
* https://developer.mozilla.org/ko/docs/WebAssembly
* https://d2.naver.com/helloworld/8786166

### 소스보안
wasm 파일은 웹구조에서 보호가 되는 것일까?

#### wasm decompile
* https://www.reddit.com/r/WebAssembly/comments/8qmxjv/can_we_decompile_wasm_to_ccsource/
* https://v8.dev/blog/wasm-decompile → decompile 되지만 거의 알아보기는 어렵다.

##### wasm to c
* https://webassembly.github.io/wabt/doc/wasm2c.1.html
* https://github.com/WebAssembly/wabt

#### wasm 파일을 public 하지 않게 사용하기?
Rust 파일 예제는 public 폴더에 복사하는 작업을 하지는 않는다.

wasm 을 웹에서 사용할때 public 하지 않게 만들 방법은 없다.
번들화된 wasm 파일도 브라우저에 다운로딩 후에 사용할 수 있다.

하지만, wasm 파일을 디컴파일해서 사용하는 것 자체가 어렵다.
입력과 출력 형태를 찾는 것도 어렵다.
wasm 파일을 제어하기 위한 파일 자체가 ugly 화 되어 있기 때문에 코드를 인식하기 어렵다.
새로 만드는 속도와 디컴파일하는 속도가 비슷할수 있다.

wasm 파일이 공개되는 것은 react component 코드가 공개될것을 두려워하지 않는 만큼 두려워 할 필요 없다.

### c 를 node module 로 동작
c 는 node 모듈로 포팅이 가능하다.
하지만 서버 사이드에서만 동작이 가능하며 클라이언트가 사용하기 위해서는 새로운 API 가 필요하다.
API 가 추가되는 것은 단순히 서버 비용이 증가하는 것만이 아니라 fetch, data syncing 등의 추가 구현이 더해진다는 것을 의미한다.
wasm 을 사용하는 것과 완전히 다른 차원의 동작을 의미한다.

### 엠스크립튼
* https://emscripten.org/index.html
* https://github.com/emscripten-core/emscripten
* `nodemon` 과 같이 자동적인 빌드 요소가 없음(또는 build bot 별도의 사용 필요?)
* 목적이 단순히 C 를 webAssembly 로 변환하는 것

#### 엠스크립튼 테스트
엠스크립튼의 결과물로 나오는 html 을 확인하려면 [light-server](https://github.com/txchen/light-server) 를 사용해야 한다.
browser 로 html 파일을 직접 읽으면 file protocol 로 열리기 때문에 XHR 동작을 사용할수가 없어 wasm 파일 로딩이 안된다.
또한 http-server 에는 mime type 설정을 아직 지원하고 있지 않아 사용할수 없다. (https://github.com/http-party/http-server/issues/35)

```sh
npm install -g light-server
light-server -s .
```

#### 엠스크립튼 + React
* `/* eslint-disable */` 를 bind js 파일에 추가해야 에러 발생을 막을수 있다.

##### `webassembly.instantiate() expected magic word` 에러
* react 는 wasm 파일을 번들화 할수 없기 때문에 wasm 파일을 public 폴더로 옮겨주어야 한다.
* bind js 의 wasm 파일 경로를 절대경로로 변경한다.
```sh
sed -i.old "s|$WASM_FILENAME|/$WASM_FILENAME|" ${JS}
```
* wasm 파일의 상대경로 위치를 설정하는 코드를 comment 한다.
```sh
sed -i.old "s|$WASM_LOOKUP|// $WASM_LOOKUP|" ${JS}
``` 

### AssemblyScript
* typeScript 를 webassembly 로 변경해주는 tool
* https://blog.scottlogic.com/2019/06/14/add-webassembly-to-react-app.html
* https://github.com/AssemblyScript/assemblyscript

### Hot Module Replacement
* blazer C# 은 가능한 것으로 보임
* webAssembly 자체로는 확인 X
* webAssembly 의 목적이 DOM 변경이 아니기 때문에 HMR 이 없는것으로 추정됨

### with React
* React 에서 엠스크립튼 결과물 사용 https://github.com/wonism/TIL/blob/master/web-assembly/emscripten/README.md
* https://medium.com/@marvinirwin/webassembly-react-and-create-react-app-8b73346c9b65
* https://koala42.com/using-webassembly-in-your-reactjs-app/
* https://www.telerik.com/blogs/using-webassembly-with-react
* https://github.com/mbasso/react-wasm
* https://github.com/rhysd/react-vim-wasm

### react-wasm Test
#### `TypeError: Incorrect response MIME type. Expected 'application/wasm'` 에러 발생
* https://community.jitsi.org/t/incorrect-response-mime-type/30439 wasm type 이 지정되어 있지 않기 때문에 에러 발생
* https://github.com/webpack/webpack-dev-middleware/issues/229
* https://github.com/mdn/webassembly-examples/issues/5
* https://github.com/facebook/create-react-app/issues/4912

#### CRA 에 `wasm` 이 기본으로 추가되지 않는 이유
* https://github.com/facebook/create-react-app/pull/7911
* webpack5 에서 아직 불안정

#### react-app-rewired
webpack extenstions 에 `wasm` 추가 필요
* https://medium.com/@jsh901220/create-react-app%EC%97%90%EC%84%9C-eject%EC%82%AC%EC%9A%A9%EC%95%88%ED%95%98%EA%B8%B0-customize-cra-react-app-rewired-10a83522ace0
* https://webpack.js.org/configuration/resolve/#resolveextensions
* https://github.com/pmrcunha/cra-wasm-test
