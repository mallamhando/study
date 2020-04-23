# Tutorial
https://www.apollographql.com/docs/tutorial/introduction

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
