### 브라우저와 코드 공유
* Node 와 크롬은 기본적으로 소스공유가능
* UMD 패턴을 통해 코그 공유가 가능, 하지만 자동화된 Webpack 이 유리

### Webpack
* 브라우저와 node.js 사이에 코드를 공유하는 방법은 다양하다. 하지만 시작은 webpack 이어야 한다.
* https://d2.naver.com/helloworld/0239818
* https://poiemaweb.com/es6-babel-webpack-2
* 모듈을 지원하지 않는 JS 에 모듈화를 지원할 수 있는 편리한 툴
* ES6 를 크로스플랫폼에서 제공해주는 편리한 툴 - import, export 등
* 난독화를 지원, facebook 소스를 알아 볼수 없는 이유는 난독화가 되어 있기 때문
* 난독화가 바이너리 암호화 수준의 변화를 의미하는 것은 아니다. 변수명을 알아볼수 없게 만들어 준다.
* ~Webpack 의 결과물은 브라우저용 번들이다. Webpack 결과물을 node.js 에서 사용할수 없다. node.js 에서는 Webpack 빌드하지 않고 그냥 사용하면 된다.~
* [server 용, front-end 용 두가지로 config 가 가능하다.](https://medium.com/code-oil/webpack-javascript-bundling-for-both-front-end-and-back-end-b95f1b429810)
* IE8 이하 외에는 모든 브라우저 지원

### 크로스 플랫폼 개발
##### 런타임 코드 분기(branching)
* 두 플랫폼의 코드가 동일한 모듈에 포함, 코드의 크기가 커짐
* 비지니스 로직이 호환성 로직과 뒤섞여 가독성이 떨어짐
* 동적 분기를 사용해 모듈을 포함시켜도 webpack 에서 인식할 수가 없어 결과적으로 코드의 크기가 커짐

##### 빌드 타임 코드 분기
* DefinePlugin 과 UglifyJsPlugin 을 이용하여 분기되어 로딩되는 모듈을 코드에 포함시키지 않을 수 있음
* 런타임 코드 분기와 마찬가지로 가독성을 떨어트린다.

##### 모듈 교환(module swapping)
책의 예제는 node.js 용 소스를 브라우저 클라이언트용으로 변경하는 것만 나와있다.

##### 크로스 플랫폼 디자인 패턴

### React
* IE 종류 빼고는 대부분 지원
* SPA(Single Page Application)의 장점이 있다. 바꿔 말하면 React 라이브러리 이전에는 큰 규모의 JS 웹클라이언트 앱을 작성하기 어려웠다는 것을 알수 있다. 불가능하다는 뜻은 아니다.
* SEO(Search Engine Optimization)에 취약해 질수 있어 별도의 방법이 필요하다.
##### React and Webpack
* React 는 Webpack, Babel, Gulp, Browserify 가 항상 필요한 것은 아니다.
* React 의 JSX 와 ES6 문법을 자유롭게 사용하는 장점을 얻기 위해서는 Webpack 이 필수적이다.
##### React Router
* React Router 를 사용하면 history 관리가 가능해진다.

### 범용 JS 앱
* React 초기 rendering 을 server 에서 진행 후 연결하면 SEO 에 적합한 site 가 될수 있다.
* API proxy 를 사용하면 api server 와 front-end 서버를 분리할 수 있다. 각각을 micro service 로 분리하기 위해서로 보인다.
  * 참고자료: https://poiemaweb.com/js-ajax#53-load-jsonp
  * JSONP, CORS, proxy 의 방법이 있지만 proxy 가 가장 진보적인 방법으로 보인다.
* express 는 왜 3000 port 를 사용하는 것일까?
* 참고자료: https://poiemaweb.com/js-spa
  * history 와 SEO 최적화는 웹 프론트엔드 최적화의 필수요소
  * SEO 최적화를 위해서는 url 분리가 필요하고,
  * url 이 분리된 상태에서 SPA 를 유지하기 위해서는 history 관리가 필요하다.
  * history 와 SEO 는 SPA 를 위해 떨어질 수 없는 주요 개념이다.

### 감상
이전에 webpack 에 대한 포스팅을 봤을때 어떤 감흥도 없었지만 이제는 이게 무엇을 말하는지 알것같다.
책에서 Webpack 을 설명했기 때문이 아니라, 그동안 쌓인 JS 지식 때문이다.
서버 기반으로 프로그래밍 하는 입장에서 브라우저 소스를 node.js 로 가져와야 겠다고 생각하고 있었는데 반대의 예만 나와서 잠깐 당황스러웠다.
반대로 브라우저 프로그래밍 하는 사람에게는 node.js 소스를 브라우저로 가져오는 쪽으로 webpack 의 기능을 착각할수 있겠다는 생각이 든다.
JS 를 알수록 다양하고 어플리케이션이 이미 존재하고 있다는 사실에 놀랄 때가 많다.
역사가 길지 않은데, 다른 언어에서 오랜 시간을 거쳐 쌓아온 노하우가 어떻게 이미 JS 안에 들어가 있는 것일까?
단순히 언어의 특성 - 구현속도가 빠르고, 편리하다 - 때문일까? 근본적으로 궁금하다.

##### React-h/w
react 의 컴포넌트 상호작용을 쉽게 보여주는 속성은 비지니스 로직 접근을 더 쉽게 해주는 장점이 있다.
하지만 이것은 Virtual-DOM 이 rendering 되는 과정이 충분히 완성되어 있고 안정적인 환경에서만 가능하다.
대부분의 h/w 구현이 직접적으로 필요한 환경에서는 React 컴포넌트 구현보다 rendering 이 h/w 에 연동되는 과정이 더 비지니스 로직에 가깝다.
반대로 고정적인 h/w 플랫폼에서는 react-h/w 는 사용가능할 수 있지만, raspberry-pi 에서는 node.js 로 충분하고, 아두이노의 활용 범위는 작다.
