### 콜백 패턴
* PHP
  * JS 처럼 스크립트형 언어
  * 비동기 동작에 차이
  * [5 top programming languages to learn server-side web development](https://twm.me/best-programming-languages-and-frameworks-for-server-side-web-development/amp/) 에 존재하고 아직 JS 보다 더 많이 사용됨
* Closure
  * [클로저 - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Closures)
  * C에서 새로운 스래드가 생기면 새로운 문맥이 생기면서 공유하는 변수 단독으로 사용하는 변수가 생겨난다. 클로저의 scope 저장은 이런 변수 공유와 분리가 자연스럽고 특별한 선언 없이 쉽게 사용될수 있게 한다. 제어가 쉬운것은 아니다 스레드와 마찬가지로 동기화의 순서를 구분하기 쉬운것은 아니다. 하지만 적어도 절차형 언어에서 발생하는 세마포어형 행사 코드는 생략될수 있다. 그 차이는 결코 작지 않다
  * Rust 같은 언어에서는 공유 변수에 대한 제어가 더 엄격하게 이루어진다. Rust 는 스레드 동작과 메모리 사용에 최적화된 언어로 여겨지는데, 그만큼 공유 변수에 대한 동작이 멀티 스레드 동작에 중요한 역할을 한다는 것을 의미한다. JS 의 유효 범위 동작은 C 의 스레드 동작보다 편하지만, 여러 Closure 들로 인해 발생하는 변수 오몀을 쉽게 추적할 수 있는 것은 아니다. Rust 언어가 어느정도 지원하는지는 사용한 뒤에 알수 있겠지만 변수에 대한 보호는 JS 자체만으로 명확하게 되지 않는다. 별도의 장치(immutable.js 라이브러리 등)가 필요하다.

### 모듈 시스템과 그 패턴

### 관찰자 패턴
