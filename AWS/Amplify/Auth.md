* https://docs.amplify.aws/cli/graphql/authorization-rules/

# Amplify GraphQL API Authorization rules
`@auth` 명령으로 public, sign-in user, per user, and per user group data access 를 설정할수 있다.
`deny-by-default` 원리로 동작한다. 즉, authorization rule 이 설정되어 있지 않으면 거절당한다.

```graphql
type Todo @model @auth(rules: [{ allow: owner }]) {
  content: String
}
```

위의 예제에서, signed-in user 인 owner 만이 자신이 생성한 Todo 에 대해서 CRUD 를 할수 있다는 것을 의미한다.
반대로 나머지 사용자는 CRUD 를 할수 없다는 것을 의미한다.

또한 각 CRUD 동작에 대해서도 허용 범위를 정할수 있다. multiple authorization rules 과 상세하게 정의된 field-level authorization 를 사용하면 된다.

```graphql
type Todo @model @auth(rules: [
  { allow: public, operations: [read]},
  { allow: owner }
]) {
  content: String
}
```

위의 예제에서 모든 사용자 public 은 Todo 에 대한 Read 권한을 가지지만 Update, Create, Delete 권한는 owner 만이 가지고 있다.

### Global authorization rule (only for getting started)
```graphql
input AMPLIFY { globalAuthRule: AuthRule = { allow: public } }
```

시작할 때는 위와 같이 public 으로 제어할수 있게 하는것이 편하다. 하지만 제품 출시 시점에는 위의 설정을 제거하고 각 model 에 맞는 권한을 설정해야 한다.

## Authorization strategies

추천하는 사용자 Case | 전략이름 | 공급자
-- | -- | --
AppSync API key 를 가지고 있는 누구나 접속 가능한 공공 데이터. | `public` | apiKey
출시 환경의 public 데이터 접근에 추천. 인증되지 않은 사용자 또는 AWS IAM 으로 권한이 확인된 장치에서 접근하는 public data. | `public` | iam
사용자 단위 데이터 접근. 각 기록의 `owner` 만이 데이터 접근 권한을 갖는다. Cognito user pool 를 기본으로 사용하는 `amplify add auth` 명령어가 있다. | `owner` | userPools / oidc
signed-in data 접근. owner-based 접근과 다르게, 모든 signed-in user 가 접근할수 있다. | `private` | userPools / oidc / iam
사용자 그룹에 따른 접근. 특정 또는 동적으로 설정된 사용자 그룹이 접근 가능 | `group` | userPools / oidc
Lambda function 으로 정의되는 custom 인증 방식 | `custom` | function

### Public data access

모두가 접근 가능한 `public` 인증 전략. 하지만, API Key 보호된다.

```graphql
type Todo @model @auth(rules: [{ allow: public }]) {
  content: String
}
```

여기에 인증 공급자를 덮어쓸수 있다. 아래의 예에서, `iam` 를 공급자로 사용하는 방법으로 Cognito identity pool 의 public access 를 이용해 API key 대신 비인증 규칙("Unauthenticated Role") 을 적용할 수 있다. `amplify add auth` 명령어를 실행하면, Amplify CLI 는 자동으로 Cognito idendity pool 에 있는 "Unauthenticated Role" 을 위한 IAM 정책으로 범위를 좁히게 된다.

```graphql
# public authorization with provider override
type Post @model @auth(rules: [{ allow: public, provider: iam }]) {
  id: ID!
  title: String!
}
```

### Per-user / owner-based data access

