# Tutorial
https://www.apollographql.com/docs/tutorial/introduction

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

