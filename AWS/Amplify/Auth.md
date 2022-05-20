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
Recommended for production environment's public data access. Public data access where unauthenticated users or devices are granted permissions using AWS IAM controls. | `public` | iam
Per user data access. Access is restricted to the "owner" of a record. Leverages amplify add auth Cognito user pool by default. | `owner` | userPools / oidc
Any signed-in data access. Unlike owner-based access, any signed-in user has access. | `private` | userPools / oidc / iam
Per user group data access. A specific or dynamically configured group of users have access | `group` | userPools / oidc
Define your own custom authorization rule within a Lambda function | `custom` | function

