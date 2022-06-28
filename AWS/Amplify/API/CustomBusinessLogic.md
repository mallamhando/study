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

