# Sign in

Auth 기능은 email/휴대폰 속성 확인, multi-factor 인증 등을 이용해 사용자 가입 기능 구현에 사용될 수 있다.
사용자 및 사용자 속성을 관리하기 위해 Amazon Cognito User Pool 이 설정된다.

## 사용자 가입

시작단계에서 사용한 기본 CLI 명령 흐름을 통해 사용자 이름, 패스워드와 유효한 email id 를 얻기 위한 설정이 될수 있다.
아래의 api 는 sign up 을 위한 동작 흐름을 수행한다.

```kotlin
val options = AuthSignUpOptions.builder()
    .userAttribute(AuthUserAttributeKey.email(), "my@email.com")
    .build()
Amplify.Auth.signUp("username", "Password123", options,
    { Log.i("AuthQuickStart", "Sign up succeeded: $it") },
    { Log.e ("AuthQuickStart", "Sign up failed", it) }
)
```

이어서 사용자 확인을 위해 sign up 과정을 수행한다. 확인 코드는 email id 를 보내고 확인 결과 응답을 받는다.

```kotlin
Amplify.Auth.confirmSignUp(
    "username", "the code you received via email",
    { result ->
        if (result.isSignUpComplete) {
            Log.i("AuthQuickstart", "Confirm signUp succeeded")
        } else {
            Log.i("AuthQuickstart","Confirm sign up not complete")
        }
    },
    { Log.e("AuthQuickstart", "Failed to confirm sign up", it) }
)
```

## Sign in a user
```kotlin
Amplify.Auth.signIn("username", "password",
    { result ->
        if (result.isSignInComplete) {
            Log.i("AuthQuickstart", "Sign in succeeded")
        } else {
            Log.i("AuthQuickstart", "Sign in not complete")
        }
    },
    { Log.e("AuthQuickstart", "Failed to sign in", it) }
)
```
