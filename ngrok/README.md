# ngrok 3.0 study

## Questions
* 보안
* 사용자 관리
* 원격 활성화/비활성화
* 비용
* Terraform

## 보안
* OAuth 접속 가능
* https 기본 접속 설정 가능

## Node.js 제어
* https://github.com/bubenshchykov/ngrok
* https://github.com/ngrok/ngrok-api-typescript

## Terraform
* https://book.naver.com/bookdb/book_detail.nhn?bid=14644648

## 기타
* 네트워크 보안으로 ngrok 이 동작 안된다면 `ngrok diagnose` 로 진단하고 custom ingress domain 을 이용해 접속할수 있다.
* local https 동작이 가능하다.
* Load Balancing
  * 에러가 발생했을때 백업으로 동작할 서버를 지정할수 있다.
  * 여러 장비들이 하나의 URL 에 묶여서 동작할수 있다.
  * URL 접속자는 여러 장비 중 하나에 접속되는 방식으로 Load Balancing 이 이루어진다.
* Domain 뿐만 아니라 TCP address 도 미리 예약할수있다.

## Link
* https://ngrok.com/
* https://ngrok.com/docs
* https://dashboard.ngrok.com/

### Terraform
* https://techblog.woowahan.com/2646/?fbclid=IwAR38ka3XoJViDTmckzqnj6OXQp1kd7ujwbDrovAanUXl4XyITDdR5GHylVE