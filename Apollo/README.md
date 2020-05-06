* [Server](./Server.md)
* [Client](./Client.md)
* [Link](./Link.md)

# Tutorial
* [tutorial](https://www.apollographql.com/docs/tutorial/introduction)
* [play ground](https://www.graphqlbin.com)

# 추가 자료
* [redux 와 apollo 단순비교](https://d2.naver.com/helloworld/4245995)
* [redux + Universal Router 와 apollo 동작 비교](https://d2.naver.com/helloworld/2838729)
* [MobX 적용기](https://woowabros.github.io/experience/2019/01/02/kimcj-react-mobx.html)
* [apollo redux mobx 비교](https://devtimothy.tistory.com/167)

# Apollo 데이터 수신 과정
1. component 에 query 가 설정되면
1. component 가 loading 될때 apollo cache 에 저장된 데이터를 먼저 확인하고,
1. 없으면 서버에서 데이터를 fetching 하여 apollo cache 에 데이터를 저장
1. component 에 데이터 전달

### server 의 데이터가 변경되었을 경우, cache update 방법

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