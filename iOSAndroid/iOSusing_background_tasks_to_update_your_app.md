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


The Xcode project editor showing the Background Modes capabilities editor of the Signing & Capabilities pane. A text callout points to the “+ Capability” button at the top-left above the editors and another callout identifies the two background mode checkbox items.

You control which tasks run in the background by registering a list of permitted task identifiers. To create this list, add the identifiers to the Info.plist file.

Open the project navigator and select your target.

Click Info and expand Custom iOS Target Properties.

Add a new item to the list and choose ”Permitted background task scheduler identifiers,“ which corresponds to the BGTaskSchedulerPermittedIdentifiers array.

Add the string for each authorized task identifier as a separate item in the array.

Illustration showing the Custom iOS Target Properties editor of the Info pane. A box is drawn around the example entries in the “Permitted background task schedule identifiers” array, which shows two identifiers. Item 0 is refresh, item 1 is .db_cleaning.

In iOS 13 and later, adding a BGTaskSchedulerPermittedIdentifiers key to the Info.plist disables the application(_:performFetchWithCompletionHandler:) and setMinimumBackgroundFetchInterval(_:) methods.


