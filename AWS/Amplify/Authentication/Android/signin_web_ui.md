# Sign in with web UI

## Configure Auth Category
> 라이버리의 Cognito plugin 은 `Authorization Code Grant` OAuth Flow 를 지원하고 있다.

`amplify add auth` 실행 이후에 아래의 옵션을 선택한다.

```bash
? Do you want to use the default authentication and security configuration? 
    `Default configuration with Social Provider (Federation)`
? How do you want users to be able to sign in? 
    `Username`
? Do you want to configure advanced settings? 
    `No, I am done.`
? What domain name prefix you want us to create for you? 
    `(default)`
? Enter your redirect signin URI: 
    `myapp://callback/`
? Do you want to add another redirect signin URI 
    `No`
? Enter your redirect signout URI: 
    `myapp://signout/`
? Do you want to add another redirect signout URI 
    `No`
? Select the social providers you want to configure for your user pool: 
    `<hit enter>`
```

## AndroidManifest.xml 수정
```xml
<queries>
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="https" />
  </intent>
  <intent>
    <action android:name=
        "android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
<application ...>
  ...
  <activity
      android:name="com.amplifyframework.auth.cognito.activities.HostedUIRedirectActivity"
      android:exported="true">
      <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="myapp" />
      </intent-filter>
  </activity>
  ...
</application>
```

logout 에 사용되는 Activity 를 위해서는 아래의 설정이 추가되어야 한다.

```xml
<activity android:name=".LogoutActivity" android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="myapp" android:host="signout" />
    </intent-filter>
</activity>
```

## Web UI Sign In 실행하기
```kotlin
Amplify.Auth.signInWithWebUI(this,
    { Log.i("AuthQuickStart", "Signin OK = $it") },
    { Log.e("AuthQuickStart", "Signin failed", it) }
)
```
