# iOS Background Task
- https://developer.apple.com/documentation/backgroundtasks

앱 컨텐츠를 최신으로 유지하거나 백그라운드 상태의 앱이 몇분이 소요되는 일을 task 를 수행하기 위해 BackgroudTasks framework 를 사용한다.
긴 수행시간의 task 는 옵션으로 추가 power 와 네트워크 연결을 요청할 수도 있다.

앱을 launche 하거나 요청된 스케쥴을 수행할 때 동작할 task 를 위해 Launch handler 를 등록한다.
OS 는 백그라운드에서 앱을 lauche 하거나 등록된 task 를 실행한다.

## `class BGTaskScheduler`
앱의 백그라운드 실행을 요청하기 위해 사용하는 스케줄링 task 클래스.
백그라운드 task 는 앱이 suspend 상태일때 앱이 코드를 실행하는 방법을 준다.

백그라운드 task 를 등록, 스케줄 설정, 실행하는 방법은 [Using_background_tasks_to_update_your_app.md](iOSusing_background_tasks_to_update_your_app.md) 를 참조한다.

## Starting and Terminating Tasks During Development


## Refreshing and Maintaining Your App Using Background Tasks

## Choosing Background Strategies for Your App
