# GraphQL 간략 설명
* 한 데이터만 가져오지 않고 관련된 데이터 묶음을 한번에 읽어오는 방법
* GraphQL API 이전에 사용하는 시스템: REST API (한 api 가 한가지 일만 하는 API 시스템)
* REST API 로 구현에 한계가 있었음, REST API 이외 별도의 API 를 매번 만들어서 사용
* 한번에 묶음 데이터를 읽어오는 방식을 규격화하고 규칙을 만듦
* API 의 규칙을 만들면 웹페이지 조합에 편리 - 항공사에 영향을 받지 않는 비행기 예약 시스템

## schema
* GraphQL API 시스템의 설계도
### type
* 기본 데이터(String, ID, Int, ..) 들의 묶음
* 선언된 type 을 다른 type 의 서브 항목으로 설정 가능
### query
* 읽기 명령어 정의
### mutation
* 수정 명령어 정의
### subscpription
* 구독 시스템 정의
* 구독 시스템 - 누군가 DB 를 수정하면 다른 앱에서 수정사항을 감지

## query 명령어
* query, mutation, subscpription 를 다양하게 변경해서 사용
* 새로운 형태의 동작을 위해 새로운 API 를 추가하지 않고 명령어 형태를 변경하는 것만으로 다른 동작이 가능

```graphql
mutation playScene($id: ID!) {
  playScene(id: $id) {
    id: _id
    play {
      mode
      scene { _id }
      scenario { _id }
      scenarios { _id }
       # 이어지는 sub type 까지 원하는 깊이 만큼 변경해서 사용가능
      schedules { _id scenarios { scenes { animation data } } }
    }
    SceneDefaultPosition { x y width height }
    AutoEnableScheduleMode
    AutoEnableScheduleModeTime
  }
}
```

### schema 를 보고 query 명령어 만드는 방법
```graphql
type Query {
  user: User
}

type User {
  id: ID!
  name: String
  photo: Photo
}

type Photo {
  id: ID!
  name: String
  url: String
}
```

위와 같이 User type 밑에 Photo type 이 있을때, 우선 명령어 부터 복사

```graphql
query {
  user: User
}
```

':' 이후 필드 type 이 String, Boolean, Int, ID 등의 기본형이 아닐 경우 type 선언을 찾아서 복사

```graphql
query {
  user: type User {
    id: ID!
    name: String
    photo: Photo
  }
}
```

`{` 이전부터 `:` 까지를 삭제

```graphql
query {
  user {
    id: ID!
    name: String
    photo: Photo
  }
}
```

String, Boolean, Int, ID 등의 기본형 type 들도 삭제

```graphql
query {
  user {
    id
    name
    photo: Photo
  }
}
```

기본형이 아닌 Photo 의 type 선언을 찾아 복사

```graphql
query {
  user {
    id
    name
    photo: type Photo {
      id: ID!
      name: String
      url: String
    }
  }
}
```

user 의 각 항목을 정리했던 것처럼 `{` 이전부터 `:` 까지를 삭제하고 기본형 type 선언도 삭제하면 

```graphql
query {
  user {
    id
    name
    photo {
      id
      name
      url
    }
  }
}
```

## resolver
* GraphQL API 하고 실제 DB 하고 연결해주는 작업

## GraphQL 사용을 위한 라이브러리
* GraphQL 은 단순히 규약이기 때문에 라이브러리를 원하는 데로 사용 가능
* Apollo, Relay(facebook)
