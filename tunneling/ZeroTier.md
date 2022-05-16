# ZeroTier
* Open Source 는 commercial 사용 금지
* 암호화된 P2P 통신 네트워크
* VPN 에 가까운 컨셉

## 질문
* [x] 양방향의 TLS 보안?
  * [x] HTTPS 와는 다르지만 데이터를 암호화함
* [x] 동시 생성 가능한 URL 개수?
* [x] iOS, Android chrome 에서 접속 가능?
* [ ] 네트워크의 수명?
* [ ] 사용자들이 쉽게 사용할수 있는 URL?
* [ ] 모든 방화벽 시스템에 사용 가능?

## Network Members
* 하나의 VPN 에 접속할수 있는 사용자 수
* 무료 계정으로 50 접속 가능

## 동시 생성 Network 수
* 무료 계정으로 제한 없음

## 사용성
* STUN 설정 없이도 WebRTC 연결 가능
* 사용자 편리성은 미지수

## 방화벽
* 완벽하게 모든 방화벽을 통과하지는 않음
* ngrok 도 유사한 문제가 있음
* 필요 만큼의 많은 방화벽을 통과 가능

## iOS & Android 접속
* ZeroTier 앱을 설치
* 사용자가 네트워크 아이디를 입력
* Admin 이 접속 허용
* 사용자가 서버 아이피를 chrome 에 입력

## 고정 연결 지점
* 고정 IP 설정 가능
* 한번 IP 가 설정되면 연결이 끊기고 다시 연결되었을때도 같은 IP 로 설정됨

## DNS 관리
* DNS 를 ZeroTier 서버에서 제공하지 않음
* DNS 서버를 network 에 추가 가능함
* Linux 의 hostname 이용한 접속은 실패

## Raspberry Pi mDNS
* mDNS 를 이용한 접속 테스트 필요
* https://bloggerbrothers.com/2017/01/08/name-your-pis-with-mdns-forget-the-ips-with-zeroconf/

## Linux Commands

### Join
```bash
sudo zerotier-cli join {networkID}
```

### Leave
```bash
sudo zerotier-cli leave {networkID}
```

## Link
* https://www.zerotier.com
* https://www.zerotier.com/pricing
* https://www.zerotier.com/2021/05/06/zeronsd-unicast-dns-resolution-for-zerotier-now-in-alpha/
* https://zerotier.atlassian.net/wiki/spaces/SD/pages/900431890/DNS+Management
