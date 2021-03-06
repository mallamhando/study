### 스트림의 중요성
이미 많은 구현과정에서 사용해 왔지만 중요성을 제대로 인지 하지 못했다.
예제로 만들어진 코드를 수동적으로 사용할뿐, 왜 이런 구조를 사용했는지를 고민하지 못했다.
시리얼.js 를 사용할때, node.js 는 시리얼을 사용할 일이 별로 없다보니 이런식의 API 밖에 제공을 안하는구나, 내가 더 발전시켜서 사용해야 겠다는 생각이 들었다.
하지만 스트림의 구조를 알게 될수록 시리얼 및 데이터 통신 입출력 구조에서 더 깊이있는 고민 속에서 만든 구조였다는 것을 알게 되었다.

### 스트림 시작하기
##### flowing vs pause mode
* pause mode 가 제어 흐름에 더 유연성을 가지고 있음, 상세 이유는 아래와 같다.
* https://nodejs.org/api/stream.html#stream_two_reading_modes
  * `Readable` stream 은 소비 또는 무시 핸들러가 설정되기 전까지 데이터를 만들어내지 않는다.
  * 만약 소비 콜백이 제거되거나 비활성화 되면 `Readable` stream 은 데이터 만들기를 정지시킨다.
  * 하지만 반대로 `data` 이벤트 핸들러가 제거되면 자동으로 `pause` 모드로 변경되지 않는다.
  * flowing 모드일때 `data` 이벤트가 없이도 background 모드로 `readable` stream 은 데이터를 계속 만들어 낸다.
  * 만약 flowing 모드일때 소비 핸들러가 없다면 데이터는 잃어버리게 된다.

### 스토림을 이용한 비동기 제어 흐름
비동기식 제어와 설명과정이 동일하다. 순차적 비동기 동작, 병렬 비동기 동작, 제한된 숫자의 병렬 비동기 동작을 각각 어떻게 구현하는지를 설명한다.

### 파이프 패턴
멀티를렉싱과 디멀티플렉싱을 사용한다. child process 에 따라 불가피할 수 있지만, 이외의 어떤 상황에서 사용해야 하는지 아직 모르겠다. 멀티/디멀티플렉싱은 부족한 통신 선로를 기반으로 이루어지는 경우가 많다. 하지만 node.js 가 실행되는 환경에서 enthernet 통신이 안되는 경우를 생각하기 어렵기 때문에 멀티플렉싱이 필요성이 느껴지지 않는다.

Web 에서도 stream 을 사용한다. stream 을 이용하여 파일 및 이미지 다운로드 하면 더 효율적일 수 있다.

f/w 단계에서 멀티/디멀티플랙싱을 사용할 수 있다면 유용성이 크게 증가할것으로 보인다.
