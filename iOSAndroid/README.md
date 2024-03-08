# iOS vs Android
## Android Handler.postDelay equivalent
- 2 초마다 반복되서 실행
- 앞의 task 가 끝나지 않으면 대기

```swift
var loop = DispatchQueue(label: "SerialQueue")

func looper() {
  loop.asyncAfter(wallDeadline: .now() + 2) {
    // do interval task
    self.looper()
  }
}

looper()
```

### iOS GCD
- https://sujinnaljin.medium.com/ios-%EC%B0%A8%EA%B7%BC%EC%B0%A8%EA%B7%BC-%EC%8B%9C%EC%9E%91%ED%95%98%EB%8A%94-gcd-grand-dispatch-queue-1-397db16d0305

```
SerialQueue.sync : 메인 스레드의 작업 흐름이 queue에 넘긴 태스크가 끝날때까지 멈춰있고(sync), 넘겨진 task는 queue에 먼저 담겨있던 작업들과 같은 스레드에 보내지기 때문에 해당 작업들이 모두 끝나야 실행 (Serial Queue)

ConcurrentQueue.sync : 메인 스레드의 작업 흐름이 queue에 넘긴 태스크가 끝날때까지 멈춰있고(sync), 넘겨진 task는 queue에 먼저 담겨있던 작업들과 다른 스레드에 보내질 수 있기 때문에 해당 작업들이 모두 끝나지 않아도 실행 (Concurrent Queue)

SerialQueue.async : 메인 스레드의 작업 흐름이 태스크를 queue에 넘기자마자 반환되고 (async), 넘겨진 task는 queue에 먼저 담겨있던 작업들과 같은 스레드에 보내지기 때문에 해당 작업들이 모두 끝나야 실행 (Serial Queue)

ConcurrentQueue.async : 메인 스레드의 작업 흐름이 태스크를 queue에 넘기자마자 반환되고 (async), 넘겨진 task는 queue에 먼저 담겨있던 작업들과 다른 스레드에 보내질 수 있기 때문에 해당 작업들이 모두 끝나지 않아도 실행 (Concurrent Queue)
```

### iOS DispatchQueue Schedule
- https://developer.apple.com/documentation/dispatch/dispatchqueue/3329230-schedule
- https://stackoverflow.com/questions/65879018/what-is-the-difference-between-dispatchqueue-schedule-vs-dispatchqueue-async
