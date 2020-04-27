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
* `Fragment` 를 이용해 query 를 나누고 합할수 있다.

### Query
* `useQuery` 는 component 가 시작될때 query 를 수행하여 데이터를 component 에 나타날수 있게 한다.
* `useQuery` 의 `fetchMore` 를 사용하면 pagination 기능을 수행할 수 있다.

### Mutation
* `useMuation` 는 mutation 하기 위한 함수와 loading 상태를 나타내는 data 값을 component 에 제공한다.
* `fetchPolicy` 를 이용해 network 에서 데이터를 가져올지 cache 에서 데이터를 query 해서 가져올지 정할수 있다.

### Local data Caching
redux 같은 library 들은 특정 상태를 caching, 저장하면서 필요한 화면을 보여준다.
예를 들어 list 항목의 한 id 를 선택하여 상세 정보를 보여주는 기능이 있을때,
선택한 id 와 화면 상태 정보를 저장해야 한다.

Apollo Client 는 `extend` 로 query data 에 local 데이터를 추가할 수 있다.
따라서 기본 query 동작을 위해 있는 caching 기능을 local 데이터에도 이용할 수 있다.

* `extend` 로 추가된 cache 데이터는 `@client` 를 이용하여 query 하여 읽어온다.
* `Virtual fields` 이해 안감, `extend` 와 어떤 차이가 있는 것인지

##### direct cache write
단순히 cache 에 데이터를 write 할때 사용한다.
`useApolloClient` 를 이용해 client 를 불러와 `client.writeData` 를 사용하거나,
mutation 함수의 onCompleted 를 이용한 callback 함수,
또는 `update` callback 을 사용하면 된다.

##### client resolvers
direct cache write 와 달리 list 에 있는 데이터를 더하거나 제거하는 복잡한 동작에 사용한다.
> server 에 수정사항을 전달한뒤에 다시 읽어오는 동작대신 cache 에 있는 데이터를 삭제하는 동작은 위험하지 않을까?
> local data 를 수정할때만 사용하기 때문인걸까?

# 추가 자료
https://d2.naver.com/helloworld/4245995

# Apollo 데이터 수신 과정
1. component 에 query 가 설정되면
1. component 가 loading 될때 apollo cache 에 저장된 데이터를 먼저 확인하고,
1. 없으면 서버에서 데이터를 fetching 하여 apollo cache 에 데이터를 저장
1. component 에 데이터 전달

### server 의 데이터가 변경되었을 경우
