# Apollo Client
* `Fragment` 를 이용해 query 를 나누고 합할수 있다.

### Query
* `useQuery` 는 component 가 시작될때 query 를 수행하여 데이터를 component 에 나타날수 있게 한다.
* `useQuery` 의 `fetchMore` 를 사용하면 pagination 기능을 수행할 수 있다.
* GraphQL 서버의 schema 에 대한 정보가 필요하지 않다. 하지만 Query 가 schema 를 벗어나면 동작하지 않기 때문에, **schema 에 대한 정보 중에서 Query 해서 읽기 위한 데이터의 graph 구조에 대해서는 반드시 알고 있어야 한다.** 라고 말할 수 있다.

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
