# Using background tasks to update your app
- https://developer.apple.com/documentation/uikit/app_and_environment/scenes/preparing_your_ui_to_run_in_the_background/using_background_tasks_to_update_your_app

백그라운드에서 앱이 task 를 수행하기 위해서 동작 시간과 소모전류 사용이 효과적으로 되도록 설정해야 한다.

## Overview

한 task 는 한 앱이 실행하는 한 동립적인 활동으로 보통 반복적으로 실행된다.
예를 들어 task 는 database 를 관리하거나 머신 러닝 모델을 정재하거나, 화면 표시 데이터를 업데이트 등의 일을 수행한다.
앱은 사용되지 않는 순간에도 동작 시간의 이점을 얻기 위해 이러한 테스트 작업을 백그라운드에서 실행하거나 시작하도록 설정할 수 있다.

백그라운드에서 실행할 task 를 스케줄 설정하기 위해, Xcode 에서 백그라운드 모드를 활성화해야 한다.
그리고 앱이 필요하기 위한 특정 task 를 확실하게 한뒤, `BGTaskScheduler` 객체에 task 를 등록한다.

## Enable and schedule background tasks

앱이 백그라운드 테스크를 수행하도록 설정하기 위해서, 원하는 백그라운드 기능을 활성화 해야 한다.
그리고 각 task 에 특정 id 를 목록을 생성한다.

두 종류의 백그라운드 task 가 있다.
`BGAppRefreshTask` 는 주식 시세를 다운로딩하는 등의 정확한 결과를 기대할수 있는 짧은 기간동안 수행되는 task 다.
`BGProcessingTask` 는 시간을 많이 소요하는 task 로, 큰 파일을 다운로드 하거나 데이터를 동기화하는 작업을 수행한다.
앱은 두 종류 하나 또는 두 종류 다를 사용할 수 있다.

백그라운드 기능을 더하기 위해서는 아래 과정이 필요하다.

1. 프로젝트 editor 를 열고 원하는 target 을 선택한다.
1. Signing & Capabilities 을 클릭한다.
1. Background Modes 섹션을 선택한다. 만약 target 에 Background Modes 섹션이 없다면, Capability 클릭하고, Background Modes 선택한다.
1. 만약 `BGAppRefreshTask` 를 사용한다면, ”Background fetch.“ 를 선택한다.
1. 만약 `BGProcessingTask` 를 사용한다면, ”Background processing.“ 를 선택한다.

![image](https://github.com/mallamhando/study/assets/30172441/787bca8f-d7f4-4a5b-87bb-b6fce1fe6592)

허용된 task id 를 목록을 등록하는 방법으로 어떤 task 가 백그라운드에서 실행되는지 제어할수 있다.
목록을 생성하기 위해서 `Info.plist` 파일에 id 를 추가한다.

1. 프로젝트 탐색기를 열고 target 을 선택한다.
1. `Info` 를 선택하고 `Custom iOS Target Properties` 를 고른다.
1. 새 아이템을 목록에 추가하고 ”Permitted background task scheduler identifiers“ 를 선택한다. 이것은 `BGTaskSchedulerPermittedIdentifiers` array 와 일치한다.
1. 각 인증된 task id 에 string 을 더해 목록에서 각각을 구분한다.

![image](https://github.com/mallamhando/study/assets/30172441/7bbcae0f-ceaf-40c7-a95f-5b81409c55f6)
