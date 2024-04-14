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
The delay between the time you schedule a background task and when the system launches your app to run the task can be many hours. While developing your app, you can use two private functions to start a task and to force early termination of the task according to your selected timeline. The debug functions work only on devices.

> Use private functions only during development. Including a reference to these functions in apps submitted to the App Store is cause for rejection.

### Starting and Terminating Tasks During Development Launch a Task

1. Set a breakpoint in the code that executes after a successful call to submit(_:).
1. Run your app on a device until the breakpoint pauses your app.
1. In the debugger, execute the line shown below, substituting the identifier of the desired task for TASK_IDENTIFIER.
1. Resume your app. The system calls the launch handler for the desired task.

```bash
e -l objc -- (void)[[BGTaskScheduler sharedScheduler] _simulateLaunchForTaskWithIdentifier:@"TASK_IDENTIFIER"]
```

### Starting and Terminating Tasks During Development Force Early Termination of a Task

1. Set a breakpoint in the desired task.
2. Launch the task using the debugger as described in the previous section.
3. Wait for your app to pause at the breakpoint.
4. In the debugger, execute the line shown below, substituting the identifier of the desired task for TASK_IDENTIFIER.
5. Resume your app. The system calls the expiration handler for the desired task.

```bash
e -l objc -- (void)[[BGTaskScheduler sharedScheduler] _simulateExpirationForTaskWithIdentifier:@"TASK_IDENTIFIER"]
```

## Refreshing and Maintaining Your App Using Background Tasks

## Choosing Background Strategies for Your App
