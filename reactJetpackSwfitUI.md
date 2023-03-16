## Declarative UI
| React | Jetpack | SwiftUI
| :--- | :--- | :--- 
| PropTypes.node | Slot API |
| children | Slot API can not be null ?
| useState | rememberSaveable | `@State`
| redux | compositionLocalOf |
| context | staticCompositionLocalOf
| css, flex, css-animation | modifier, Column, Row, Animation
| async | coroutine
| canvas | canvas
| Class Controller | ViewModel
| IndexedDB | SQLite
| redux, useQuery | Room | ObservableObject
| router-dom | Navigation
| useState, redux | Kotlin Flow
| MUI | Compose Theme
| npm | Gradle
| useEffect | SideEffect, LaunchedEffect | `.onAppear`, `.onChange(of: state)`

당연히 모든 기능들이 100 % 일치하지는 않는다. 특히 아키텍처와 관련된 기능이 그렇다.
최대한 단순한 기능으로 부터 시작하고,
구현이 심화될수록 아키텍처를 적용하려고 해야 한다.

## Language
| 용도 | typescript | kotlin | swift
| :--- | :--- | :--- | :--- 
| 상수 | const | val | let
| 변수 | let | var | var
| 리터럴 템플릿 | ``${str}`` | `"{str}"` | `"\(str)"`
| 튜플 | | | `(a, 10, "str")` 
| 옵셔널 타입 |
| 타입 캐스팅 
| switch |
| for |
| 함수 | function | fun | func
| 함수 인자 |
| 클로저 |
| 클래스 |
## 자료
* https://velog.io/@dev-junku/Android-Naver-Android-Jetpack-Compose-%EC%A0%81%EC%9A%A9-%ED%9B%84%EA%B8%B0-%EB%B0%9C%ED%91%9C-%EC%A0%95%EB%A6%AC
* https://developer.android.com/codelabs/basic-android-kotlin-compose-viewmodel-and-state?hl=ko#3
