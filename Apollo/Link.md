# Apollo Link
* https://www.apollographql.com/docs/link/
* https://www.daleseo.com/graphql-apollo-link-schema/

##### 질문과 답
* SSR 에서 Apollo Client 가 필요한 이유
  * mockup data 가 필요해서?
* 다른 서버의 graphql 을 가져오는 방법
  * `npx apollo schema:download --endpoint=http://localhost:4040/graphql schema.json`

### Apollo Link HTTP
Apollo Client 가 GraphQL 서버의 데이터를 가져오는 최적의 방법과 도구를 제공

### Apollo Link Schema
Apollo Client 가 SSR 환경의 서버단에서 GraphQL 의 데이터를 읽어오지 않고도 동작될수 있게함
