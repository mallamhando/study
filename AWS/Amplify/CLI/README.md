# Amplify CLI
## `amplify configure`
amplify 를 설정한다.

## `amplify init`
새로운 amplify 프로젝트를 생성한다.

## `amplify pull --appId {xxxx} --envName {envName}`
기존에 만들었던 amplify 프로젝트를 불러온다.
appId 와 envName 은 AWS Console 의 Amplify 에서 확인할 수 있다.

## `amplify mock`
### `amplify mock api`
local api server 가 실행된다.

### `amplify mock {lambda} --event src/event.json`
lambda 함수가 실행된다.

## `amplify push -y`
amplify 가 AWS 에 배포된다.
