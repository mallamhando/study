### 브라우저와 코드 공유
UMD 패턴이 있지만 자동화가 가능한 Webpack 이 유리

### Webpack
* https://d2.naver.com/helloworld/0239818
* https://poiemaweb.com/es6-babel-webpack-2
* 모듈을 지원하지 않는 JS 에 모듈화를 지원할 수 있는 편리한 툴
* ES6 를 크로스플랫폼에서 제공해주는 편리한 툴
* 난독화를 지원, facebook 소스를 알아 볼수 없는 이유는 난독화가 되어 있기 때문
* 난독화가 바이너리 암호화 수준의 변화를 의미하는 것은 아니다. 변수명을 알아볼수 없게 만들어 준다.
* ~Webpack 의 결과물은 브라우저용 번들이다. Webpack 결과물을 node.js 에서 사용할수 없다. node.js 에서는 Webpack 빌드하지 않고 그냥 사용하면 된다.~
* [server 용, front-end 용 두가지로 config 가 가능하다.](https://medium.com/code-oil/webpack-javascript-bundling-for-both-front-end-and-back-end-b95f1b429810)

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
##### React and Webpack
* React 는 Webpack, Babel, Gulp, Browserify 가 항상 필요한 것은 아니다.
* React 의 JSX 와 ES6 문법을 자유롭게 사용하는 장점을 얻기 위해서는 Webpack 이 필수적이다.

### 범용 JS 앱

### 감상
이전에 webpack 에 대한 포스팅을 봤을때 어떤 감흥도 없었지만 이제는 이게 무엇을 말하는지 알것같다.
책에서 Webpack 을 설명했기 때문이 아니라, 그동안 쌓인 JS 지식 때문이다.
서버 기반으로 프로그래밍 하는 입장에서 브라우저 소스를 node.js 로 가져와야 겠다고 생각하고 있었는데 반대의 예만 나와서 잠깐 당황스러웠다.
반대로 브라우저 프로그래밍 하는 사람에게는 node.js 소스를 브라우저로 가져오는 쪽으로 webpack 의 기능을 착각할수 있겠다는 생각이 든다.
