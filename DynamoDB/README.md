# DynamoDB를 사용한 설계 및 아키텍처 설계 모범 사례
https://docs.aws.amazon.com/ko_kr/amazondynamodb/latest/developerguide/bp-general-nosql-design.html

## DynamoDB를 위한 NoSQL 설계
### 관계형 데이터 설계와 NoSQL의 차이점
단일 엔터티를 쿼리하는 것은 DynamoDB 가 유리하지만 관계형 데이터를 쿼리할대는 RDBMS 가 더 유리하다고 설명하는 것 같다.
자세한 내용은 아직 부족하다.

### NoSQL 설계의 두 가지 주요 개념
* DynamoDB 애플리케이션에서는 가능한 적은 수의 테이블을 유지

PostageSQL 로 갈까? ㅠㅜ
IoT 데이터는 DynamoDB 가 적합해 보이지만, 사용자 관계를 표현할때는 SQL 이 편리할것 같은 느낌이다.

### NoSQL 설계에 접근
쿼리 패턴 파악이 중요

## 효과적으로 파티션 키를 설계해 사용하는 모범 사례
