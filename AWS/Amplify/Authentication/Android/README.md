# Amplify Android Library Authentication

Amplify Auth 기능은 사용자 인증 인터페이스와 함께
백그라운드 동작으로 다른 Amplify 부분에 인증 결과를 제공한다.
Amazon Cognito User Pool 과 Identity Pool 을 기본적으로 사용한다.
Amplify CLI 는 인증 지능을 생성하고 설정하는데 도움을 준다.

## 목적
인증 기능의 목적은 앱에 Amplify Auth 기능을 설정하고 현재의 인증 session 을 확인하기 위한 간단한 api 를 제공하는 것이다.

## 선행 요소
Android 앱은 최소 Android SDK API level 16 이상을 사용해야 한다.

## 인증 기능 설정

아래 명령어로 인증 resource 제공을 시작할수 있다.
```bash
amplify add auth
```

```bash
? Do you want to use the default authentication and security configuration?
    `Default configuration`
? How do you want users to be able to sign in?
    `Username`
? Do you want to configure advanced settings?
    `No, I am done.`
```

```bash
amplify push
```

위의 명령어가 완료되면 backend 인증 리소스 제공을 위한 설정인 `amplifyconfiguration.json` 파일이 업데이트 된다.

## Amplify Libraries 설치

아래의 dependency 를 앱의 `build.gradle` 파일에 추가하고, "Sync Now" 를 클릭한다.
```gradle
dependencies {
    implementation 'com.amplifyframework:aws-auth-cognito:1.35.4'
}
```

## Amplify Auth 초기화

앱 시작 시에 `Amplify.configure` 명령어로 라이브러리를 초기화 한다.

```kotlin
// Add this line, to include the Auth plugin.
Amplify.addPlugin(AWSCognitoAuthPlugin())
Amplify.configure(applicationContext)
```

## 현재의 auth session 확인

```kotlin
Amplify.Auth.fetchAuthSession(
    { Log.i("AmplifyQuickstart", "Auth session = $it") },
    { error -> Log.e("AmplifyQuickstart", "Failed to fetch auth session", error) }
)
```
