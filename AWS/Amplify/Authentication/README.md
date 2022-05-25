* https://docs.amplify.aws/cli/auth/overview/

# Overview

Amplify CLI 는 여러 종류의 인증 작업 설정을 지원한다. 단순하거나 고급 설정의 로그인을 포함해, 다른 생애주기 이벤트 동안에 동작되는 Lambda 함수들과 어플리케이션에 적용할수 있는 추가적인 관리자인 동작을 지원한다.

* [User groups](groups.md)
* [Admin actions](admin.md)
* [Use an existing Cognito User Pool and Identity Pool](import.md)
* [Override Amplify-generated Cognito resources](override.md)

## Configuring auth without social providers

시작하기 위한 가장 간단한 방법은 대부분의 경우에 적용 가능한 기본 설정을 가지고 있는 명령어를 사용하는 것이다.

```bash
amplify add auth  ## "amplify update auth" if already configured
```

```bash
Do you want to use the default authentication and security configuration? 
❯ Default configuration 
  Default configuration with Social Provider (Federation) 
  Manual configuration 
  I want to learn more.
```

## Configuring auth with cocial providers

User Pool 이 동작된다면 Facebook, Google, Amazon 로그인을 이용한 로그인을 활성화 시킬수 있다. *Manual Configuration* 을 사용하면 더 자세한 설정이 가능하다.

```bash
amplify add auth  ## "amplify update auth" if already configured
```

기본 설정으로 Social Provider 를 선택한다.

```bash
Do you want to use the default authentication and security configuration?
  Default configuration
❯ Default configuration with Social Provider (Federation)
  Manual configuration
  I want to learn more.
```

## Re-use an existing Cognito User Pool and Identity Pool

Amplify CLI 가 새로운 auth resource 를 생성하는것 대신 이미 가지고 있는 Cognito resource 를 적용할수 있다. 이런 resource 들은 자동 생성되는 Amplify 라이브러리 설정 파일을 이용해 사용될수 있다. 또한 다른 영역들의 auth 의존성과 Lambda 함수를 통한 접근 허가 기능도 제공한다.

`amplify import auth` 를 사용하거나 [guide on how to import existing Cognito resources](https://docs.amplify.aws/cli/auth/import/) 에 자세한 내용이 설명되어 있다.
