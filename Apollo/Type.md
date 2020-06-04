* https://www.apollographql.com/docs/graphql-tools/scalars/
* https://github.com/taion/graphql-type-json

### Trivial resolver - Type resolver
type 의 필드가 채워지지 않았다면 Trivial resolever 를 호출한다.
리스트 필드 안에 리스트 필드가 또 존재할수 있다.
타입 리졸버가 없으면 쿼리 리졸버에서 리스트 필드안에 있는 리스트 필드를 채워줘야 하는 재귀적 문제가 발생한다.
또한 쿼리에서 해당 리스트 필드를 호출하지 않을 때도 쿼리 리졸버에서 해당 리스트 필드를 채우는 동작하는 비효율이 발생한다.
