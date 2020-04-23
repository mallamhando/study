# Tutorial
https://www.apollographql.com/docs/tutorial/introduction

# Apollo Server
기존 DB 또는 API 를 이용해서 graphQL API 를 생성하는 서버

> Apollo Client 만 있는 경우, PWA 에서는 누가 Apollo Server 역할을 할까?

### schema
* 사용자가 원하는 행위를 정의하고, schema 를 작성
* schema-first development: back-end 와 front-end 사이의 협업이 필요

schema type 선언중 `!` 표시는 null 값이 될수 없다를 의미한다.
mongoose schema 의 isRequired 와 같은 의미로 보인다.
array type 과 `!` 를 사용하면, null 이 될수 없지만 empty array 는 될수 있다.

##### Query type
* data graph 는 DB schema, query 는 사용자가 원하는 데이터 형태

##### Mutation type
* query 는 읽기 전용, mutation 은 수정 데이터

https://github.com/prisma-labs/graphql-playground/ 으로 테스트 가능

### DataSource
* 다양한 형태의 db, API 또는 비지니스 로직하고 결합이 가능

### Resolver
* Resolver 는 query, mutation, subscription 등의 GraphQL 의 동작을 데어터로 변환하는 기능들을 제공한다.
* Resolver 는 schema 와 동일한 타입의 데이터 또는 데이터를 위한 promise 객체를 return 한다.

##### Resolver 함수 형태
```JS
fieldName: (parent, args, context, info) => data;
```
* parent: parent type 의 resovler 에서 return 한 결과물을 포한한 객체
* args: field 에 전달되는 args
* context: GraphQL 동작의 모든 resolver 들에게 전달되는 데이터. 인증 데이터나 데이터 소스에 대한 접근 상태를 저장할 수 있다.
* info: 동작에대한 수행 상태를 나타내는 정보, 상세 고급 동작에 쓰인다.

##### Pagination
* 한번에 많은 데이터 Set 을 가져오는것은 어플리케이션을 느리게 함
* resolver 를 통해 Cursor-based pagination 가능

##### Type
* schema 의 모든 type 에 대한 resolver 가 있다.
* type 에 대한 resolver 를 정하지 않고, 부모 객체가 같은 이름의 속성을 가지고 있다면 graphQL 의 기본 resolver 가 동작된다.
* type 에 대한 resolver 가 적절하게 설정되지 않으면 기본 resolver 가 있어도 데이터가 제대로 읽히지 않는다.

##### 사용자 인증
* 사용자 인증 mutation 동작을 하면 인증 토큰을 얻게 된다.
* 인증 토큰을 http header 에 넣은 뒤에 mutation 을 해야 사용자와 관련된 mutation 동작이 context 를 통해 id 를 받아 실행한다.

# Apollo Client
