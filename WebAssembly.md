* [언제 웹어셈블리를 사용하는 게 좋은가](https://engineering.huiseoul.com/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%9E%91%EB%8F%99%ED%95%98%EB%8A%94%EA%B0%80-%EC%9B%B9%EC%96%B4%EC%85%88%EB%B8%94%EB%A6%AC%EC%99%80%EC%9D%98-%EB%B9%84%EA%B5%90-%EC%96%B8%EC%A0%9C-%EC%9B%B9%EC%96%B4%EC%85%88%EB%B8%94%EB%A6%AC%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EA%B2%8C-%EC%A2%8B%EC%9D%80%EA%B0%80-cf48a576ca3)
* https://developer.mozilla.org/ko/docs/WebAssembly
* https://d2.naver.com/helloworld/8786166

### 엠스크립튼
* https://emscripten.org/index.html
* https://github.com/emscripten-core/emscripten
* `nodemon` 과 같이 자동적인 빌드 요소가 없음(또는 build bot 별도의 사용 필요?)
* 목적이 단순히 C 를 webAssembly 로 변환하는 것

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
