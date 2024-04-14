# iOS Background Task
- https://developer.apple.com/documentation/backgroundtasks

앱 컨텐츠를 최신으로 유지하거나 백그라운드 상태의 앱이 몇분이 소요되는 일을 task 를 수행하기 위해 BackgroudTasks framework 를 사용한다.
긴 수행시간의 task 는 옵션으로 추가 power 와 네트워크 연결을 요청할 수도 있다.

앱을 launche 하거나 요청된 스케쥴을 수행할 때 동작할 task 를 위해 Launch handler 를 등록한다.
OS 는 백그라운드에서 앱을 lauche 하거나 등록된 task 를 실행한다.

## `class BGTaskScheduler`
앱의 백그라운드 실행을 요청하기 위해 사용하는 스케줄링 task 클래스.
백그라운드 task 는 앱이 suspend 상태일때 앱이 코드를 실행하는 방법을 준다.

백그라운드 task 를 등록, 스케줄 설정, 실행하는 방법은 [Using_background_tasks_to_update_your_app](iOSusing_background_tasks_to_update_your_app.md) 를 참조한다.

## Starting and Terminating Tasks During Development
- https://developer.apple.com/documentation/backgroundtasks/starting-and-terminating-tasks-during-development

개발할 때 task 를 시작하기 위해 debugger 를 사용하고 완료전에 debugger 를 종료하는 방법

### Starting and Terminating Tasks During Development Overview

백그라운 task 를 스케줄 설정하는 시간과 시스템이 app 을 launch 하고 task 를 실행하는데는 많은 지연시간이 필요하다.
반면 앱을 개발할때는, 두개의 private 함수를 이용해 미리 선택한 시간대역에서 task 를 시작하고 강제로 빨리 종료 시킬수 있다.
debug 함수는 devices 에서만 동작된다.

> private 함수는 개발할때만 사용해야 한다. 이 함수들을 참조하는 것을 포함한 앱은 App Store 에 등록하는것을 거절하게 되어 있다.

### Starting and Terminating Tasks During Development Launch a Task

1. `submit(_:)` 이 성공적으로 호출된 직후의 위치에 breakpoint 를 설정한다.
2. breakpoint 에서 멈출때까지 앱을 실행시킨다.
2. debugger 안에서 원하는 task 의 TASK_IDENTIFIER 를 이용해 아래의 명령어를 입력해서 실행시킨다.
3. app 을 재실행시키면 시스템은 원하는 task 의 launch handler 를 호출한다.

```bash
e -l objc -- (void)[[BGTaskScheduler sharedScheduler] _simulateLaunchForTaskWithIdentifier:@"TASK_IDENTIFIER"]
```

### Starting and Terminating Tasks During Development Force Early Termination of a Task

2. 원하는 task 에 breakpoint 를 설정한다.
4. 위에 서술된 방법으로 debugger 이용해 task 를 실행시킨다.
6. 앱이 breakpoint 에서 멈추게될때까지 실행한다.
2. debugger 안에서 원하는 task 의 TASK_IDENTIFIER 를 이용해 아래의 명령어를 입력해서 실행시킨다.\
3. app 을 재실행시키면 시스템은 원하는 task 의 expiration handler 를 호출한다.

```bash
e -l objc -- (void)[[BGTaskScheduler sharedScheduler] _simulateExpirationForTaskWithIdentifier:@"TASK_IDENTIFIER"]
```

## Refreshing and Maintaining Your App Using Background Tasks

## Choosing Background Strategies for Your App
