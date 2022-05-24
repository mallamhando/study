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

데이터 접근을 특정 사용자에게 특정하기 위해서는 `owner` 인증 전략이 필요하다. `owner` 인증을 사용하면, 오직 데이터 `owner` 만이 특정 동작을 수행할 수 있다.

```graphql
# The "owner" of a Todo is allowed to create, read, update, and delete their own todos
type Todo @model @auth(rules: [{ allow: owner }]) {
  content: String
}

# The "owner" of a Todo record is only allowed to create, read, and update it.
# The "owner" of a Todo record is denied to delete it.
type Todo @model @auth(rules: [{ allow: owner, operations: [create, read, update] }]) {
  content: String
}
```

Amplify 는 자동으로 `owner: String` 필드를 각 데이터에 더한다. 각 데이터의 `owner` 필드는 `owner` 의 식별 정보를 저장한다.

기본적으로, Cognito user pool 의 사용자 정보는 `owner` 필드에 덧붙여진다. `sub` 와 `username` 이 `<sub>::<username>` 형식의 값으로 사용된다. API 는 `<sub>::<username>` 또는 분리된 `sub` / `username` 값을 이용해 인증하고 `username` 으로 응답한다. 또는, [OpenID Connect as an authorization provider](https://docs.amplify.aws/cli/graphql/authorization-rules/#using-oidc-authorization-provider) 를 사용할 수도 있다.

인증 규직에서 custom `ownerField` 를 특정하는 방법으로 `owner` 필드를 선호하는 다른 필드로 덮어쓸수도 있다.

```graphql
type Todo @model @auth(rules: [{ allow: owner, ownerField: "author" }]) {
  content: String                             #^^^^^^^^^^^^^^^^^^^^
  author: String # record owner information now stored in "author" field
}
```

기본적으로 한 사용자만 데이터의 owner 가 될수 있지만, 사용자 집합으로 데이터 접근 권한을 설정할 수도 있다.

```graphql
type Todo @model @auth(rules: [{ allow: owner, ownerField: "authors" }]) {
  content: String
  authors: [String]
}
```

위의 예시에서 `authors` 목록이 데이터 생성시 덧붙여진다. 생성자는 `authors` 필드를 추가적인 사용자로 업데이트 할수 있다. `authors` 필드의 모든 사용자는 데이터에 접근할수 있다.

**Known limitation**: `authors` 목록으로 설정되면 `owner` 가 설정될때 실시간 subscription 기능이 지원되지 않는다.

### Signed-in user data access

데이터 접근이 모든 signed-in 사용자에게로 제한될때, `private` 인증 전략을 사용한다.

> 특정 사용자에게만 접근 권한을 설정하고 싶으면 [Per-user / owner-based data access](#per-user--owner-based-data-access) 를 사용해야 한다. signed-in 방식은 signed-in 한 모든 사용자에게 접근 가능한 인증 규칙이다.

```graphql
type Todo @model @auth(rules: [{ allow: private }]) {
  content: String
}
```

우의 예제에서 Cognito user pool 를 통해 유효하다고 인증받은 JWT 토큰 사용자는 모든 Todo 데이터에 접근 가능하다.

인증 공급자를 변경할 수도 있다. 아래의 예에서 API key 대신 public 접근을 위한 Cognito identity pool 의  `iam` 인증 규칙이 공급자로 설정되었다. `amplify add auth` 를 실행하면 Amplify CLI 는 자동으로 Cognito identity pool 에서 인증규칙을 위한 IAM 정책 대로 접근 범위를 제한한다.

```graphql
type Todo @model @auth(rules: [{ allow: private, provider: iam }]) {
  content: String
}
```

또한, OpenID Connect 으로도 `private` 인증을 사용할수 있다. 자세한 내용은 [OpenID Connect as an authorization provider](https://docs.amplify.aws/cli/graphql/authorization-rules/#using-oidc-authorization-provider) 을 참조해야 한다.

### User group-based data access

사용자 그룹으로 데이터 접근 권한을 제한하고 싶을때 `gruop` 인증 전략을 사용한다.

**Static group authorization**: 특정 사용자 그룹으로 제한하고 싶을때, `groups` 인자에 그룹명을 입력한다.

```graphql
type Salary @model @auth(rules: [{ allow: groups, groups: ["Admin"] }]) {
  id: ID!
  wage: Int
  currency: String
}
```

위의 예에서 "Admin" 그룹의 사용자만이 Salary 모델 데이터에 접근할수 있다.

**Dynamic group authorization**: 동적으로 접근가능한 사용자 그룹을 변경하고 싶을때 사용한다.

```graphql
# Dynamic group authorization with multiple groups
type Post @model @auth(rules: [{ allow: groups, groupsField: "groups" }]) {
  id: ID!
  title: String
  groups: [String]
}

# Dynamic group authorization with a single group
type Post @model @auth(rules: [{ allow: groups, groupsField: "group" }]) {
  id: ID!
  title: String
  group: String
}
```
