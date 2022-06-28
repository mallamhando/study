# Custom business logic (Lambda function, HTTP, and VTL resolvers)

Lambda 함수 resolver, HTTP resolver, VTL resolver 를 사용하여 custom business logic 을 정의한 뒤에 GraphQL query 또는 mutation 으로 배포할수 있다.
확장 또는 덮어 쓰여진 Amplify 생성 GraphQL resolver 들은 특정한 use case 를 최적화 할수 있다.

## Create a custom query or mutation

`@model` 이 자동으로 CRUD+subscription 을 생성하는 반면, 어떤 경우에는 단독적인 query 와 mutation 을 사용할 필요도 있다.

1. custom query 와 mutation 을 정의

```graphql
type Mutation {
  myCustomMutation(args: String): String # your custom mutations here
}

type Query {
  myCustomQuery(args: String): String # your custom queries here
}
```

2. 아래의 resolver 들 중에서 한가지를 선택해 query 와 mutation 에 사용할 수 있다.

* Lambda function resolver
* HTTP resolver: HTTP endpoint 를 query 와 mutation 으로 덮어 사용한다.
* VTL resolver (most advanced)

3. field-level authorization rules 을 이용해 custom query 과 mutation 에 보안을 더한다.

* Dynamic 인증 규칙은 custom query/mutation 에 사용될 수 없다.

## Lambda function resolver

`@function` 지시문은 빠르고 쉽게 AWS Lambda resolver 를 GraphQL API 에 설정하는 방법이다.
Amplify CLI, AWS Lambda console 또는 다른 어떤 도구로 생성된 AWS Lambda 함수들도 사용이 가능하다.

예로 `amplify add function` 를 이용해 아래와 같은 handler 를 사용하는 "echofunction" 이란 이름의 Lambda 함수를 더한다.

```JS
exports.handler =  async function(event, context){
  return event.arguments.msg;
};
```

AWS Lambda resolver 를 GraphQL API 와 연결하기 위해 `@function` 지시문을 schema 에 더한다.

```graphql
type Query {
  echo(msg: String): String @function(name: "echofunction-${env}")
}
```

Amplify CLI 다양한 환경을 제어하는 방법을 제공한다.
`amplify add function` 를 통해 배포할때, 자동으로 Lambda 함수이름에 환경 변수를 더해준다.

### 함수 이벤트 구조

연결된 Lambda 함수는 아래와 같은 AWS Lambda `event` 구조체를 받게된다.

| Key	| Description |
| :--- | :--- |
typeName | query 필드 resolver 의 부모 객체 타입 이름
fieldName | resolve 되는 필드 이름
arguments | query 되고 있는 입력 변수
identity | 요청에 포함된 사용자 정보 map. 'claims' 으로 정의된 변수에는 JWT claims 등을 포함할 수 있다.
source | 중첩된 필드의 resolver 동작을 할경우 부모 객체의 실제 값. 예를 들어 Post.comment 를 호출할 경우 comment 리졸버에서 Post 객체를 확인하는 것을 의미한다.
request | AppSync request 객체. header 정보를 가지고 있다.
prev | pipeline resolver 를 사용할때, 이전 함수에서 전달한 값을 의미한다. use case 에 따라 이전 값을 검증할 수 있다.

### Chaning functions

여러개의 `@function` 리졸버를 차례로 실행되게 할수 있다.
pipeline 리졸버를 구성하기 위해 여러개의 `@function` 지시어를 더하면 된다.

```graphql
type Mutation {
  doSomeWork(msg: String): String @function(name: "worker-function") @function(name: "audit-function")
}
```

위와 같은 설정에서 **worker-function** 이후에 **audit-function** 이 실행된다.
**worker-function** 의 결과는 **event.prev.result** 에 저장되어 전달된다.

### How it wokrs

`@function` 지시어의 정의는 다음과 같다.

```graphql
directive @function(name: String!, region: String) on FIELD_DEFINITION
```

Amplify 는 문서안의 `@function` 의 특정 인스턴트마다 `AppSync::FunctionConfiguration` 를 생성한다.
그리고 pipeline 리졸버가 각 `@function` 를 가르키도록 한다.

`@function` 지시어는 필요할 경우 아래 리소스를 생성한다.

1. AWS AppSync trust policy 의 수행 권항용 AWS IAM role
2. 새로운 역할과 이미 존재하는 API 함수를 등록하기 위한 AWS AppSync data source
3. 새로운 데이터 소스와 Lambda event 를 대비하기 위한 AWS AppSync pipeline function
4. 새로운 pipeline 함수를 실행하고 GraphQL 필드와 연결하기 위한 AWS AppSync resolver
