# Tutorial
* [tutorial](https://www.apollographql.com/docs/tutorial/introduction)
* [play ground](https://www.graphqlbin.com)

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
