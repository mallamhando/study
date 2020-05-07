# Apollo Link
* https://www.apollographql.com/docs/link/
* https://www.daleseo.com/graphql-apollo-link-schema/

Apollo Link 는 GraphQL 의 결과를 얻는 방법과 그 결과로 하고 싶은것을 해주는 방법이다.
Apollo Link 는 Apollo Client, graphql-tools 의 schema 만들기, GraphiQL, Client 단독 프로그램등과 사용될수 있기 때문에 동일한 인증, 동일한 error 핸들링, 동일한 GraphQL fetching 제어 방식을 제공할 수 있다.

retries, live queries, 다양한 transports, 대안적인 caching layers, and offline support 를 만족하기 위해서는 Apollo Client 하나로는 불가능하기 때문에 별도의 library 를 만드는 것이 Apollo Link 의 목적이다.

##### 질문과 답
* SSR 에서 Apollo Client 가 필요한 이유
  * mockup data 가 필요해서?
* 다른 서버의 graphql 을 가져오는 방법
  * `npx apollo schema:download --endpoint=http://localhost:4040/graphql schema.json`

### Apollo Link HTTP
Apollo Client 가 GraphQL 서버의 데이터를 가져오는 최적의 방법과 도구를 제공

### Apollo Link Schema
Apollo Client 가 SSR 환경의 서버단에서 GraphQL 의 데이터를 읽어오지 않고도 동작될수 있게함
