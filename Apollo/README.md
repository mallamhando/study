* [Server](./Server.md)
* [Client](./Client.md)
* [Link](./Link.md)
* [Cache](./Cache.md)
* [File](./File.md)

# Tutorial
* [tutorial](https://www.apollographql.com/docs/tutorial/introduction)
* [play ground](https://www.graphqlbin.com)

# 추가 자료
* [GraphQL with indexedDB](https://codeburst.io/large-offline-datasets-with-apollo-client-c19c987e4a98)
##### 국내 사용 후기
* [apollo redux mobx 비교](https://devtimothy.tistory.com/167)
* [redux 와 apollo 단순비교](https://d2.naver.com/helloworld/4245995)
* [redux + Universal Router 와 apollo 동작 비교](https://d2.naver.com/helloworld/2838729)
* [MobX 적용기](https://woowabros.github.io/experience/2019/01/02/kimcj-react-mobx.html)
* [local note 만들기](https://coding-dahee.tistory.com/117)
* [원격에서 schema 가져오기](https://www.daleseo.com/graphql-react-apollo-hooks/)
* [초보 후기](https://medium.com/@han7096/graphql-%EA%B3%BC-apollo%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EB%A9%B0-%EC%A4%91%EA%B0%84-%EC%A0%95%EB%A6%AC-42981522b188)

# Apollo 데이터 수신 과정
1. component 에 query 가 설정되면
1. component 가 loading 될때 apollo cache 에 저장된 데이터를 먼저 확인하고,
1. 없으면 서버에서 데이터를 fetching 하여 apollo cache 에 데이터를 저장
1. component 에 데이터 전달

### server 의 데이터가 변경되었을 경우, cache update 방법
* https://www.apollographql.com/docs/react/data/queries/#updating-cached-query-results
* mutation 의 `refetchQueries` 옵션
* query 의 `fetchPolich` 옵션
* client 의 `resetStore` 함수는 cache 를 clear 한다.

# 실사용 후기
##### express 미들웨어 동작에 대한 이해가 부족해 발생한 실수
* express.router 와 API error 핸들러를 express 미들웨어에 등록한 뒤에 apollo-server 를 등록하면 `/graphql` 경로에 대한 API error 가 발생
* 새로운 API 는 API error 핸들러 이전에 등록해야 에러가 발생하지 않는다.
##### dataSource
* 필수요소는 아니다. dataSource 로 작업할수 있다면 apollo 와의 결합성이 더 나아지는 것으로 보인다.
##### resolver 만들기
* graphQL 를 쓰면 편리한 query 와 mutation 가 알아서 따라 올것 같지만, 실은 resolver 로 그 편리한 기능을 다 만들어줘야 한다.
* graphQL 과 apollo 는 편리하게 사용하기 위한 규칙, 특을 제공한다고 보는게 더 맞을것 같다.
* 하지만 어려운 resolver 를 구현하고 나면 정말 편리하게 사용할수 있다.
* 편리한 규칙은 그냥 만들어지지 않는다. 무게감 있는 형식이 느껴진다.
##### react-starter-kit
* https://github.com/kriasoft/react-starter-kit
* apollo feature 의 update 가 오래되어 동작되지 않는다.
* apollo client 가 없어도 동작할수 있도록 fetch 를 hook 하여 `/graphql` url 일때만 graphql 이 동작되도록 되어 있다.
##### Apollo cache immutable
* mutation 하지 않으면 cache 의 데이터가 변경되지 않는다.
* redux + immer.js 과 같은 구성이 필요없다.
##### Schema driven development
* schema 를 위한 프로그램이 아니라, schema 설계에서부터 사용자의 요구사항이 제대로 반영되어야 한다.
* schema, resolver 가 client 와 server 모두에 쉽게 공유되고 쉽게 수정될수 있어야 한다.
* 즉, 프로그램의 schema 의존성을 피할 왕도는 없다. schema 의 작은 부분이라도 변경되면 전체 소스의 변화량은 크다. 최대한 적게 변할수 있도록 잘 짜는 수밖에 없다.
##### 학습 시간
* 길다. redux, fetch, immer 등을 사용하는 수준이 아니다.
##### with redux
* 기존 redux 로 동작하던 코드를 반드시 apollo client cache 로 변경할 필요가 없었다.
* 프로젝트의 특성상 schema 의 일부 데이터는 변하기 쉬운 Object type 이었다. Query 와 schema 를 계속 수정하는 것보다 redux 를 사용하는 것이 간단했다.
##### graphql API 변경
* https://www.apollographql.com/docs/link/links/http/#passing-context-per-query
* `client.query` 에 context 로 다른 API 를 입력하는 방식으로 graphql 의 API 를 변경할 수 있다.
##### schema 와 resolver
* 실수로 schema 없이 resolver 만 작성한 부분이 있었는데 정상적으로 동작했다.
* 버그인지 원래 컨셉인지 알수는 없다.
##### indexedDB 와 API 는 다른 Query
* indexedDB 와 API 는 다른 Query 를 사용해야 데이터 중첩 문제가 발생하지 않는다.
