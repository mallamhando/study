# JobScheduler
* https://developer.android.com/reference/kotlin/android/app/job/JobScheduler

다양한 타입의 일들을 framework 에게 앱이 소유한 프로세스를 실행하는 방식으로 스케줄링으로 실행하기 위한 API 입니다.

`android.app.job.JobInfo` 를 보면 어떤 타입의 job 을 정의할수 있으며 어떻게 구성하는지 알수 있습니다.
JobInfo 객체를 구성하면 JobScheduler 에 `schedule(android.app.job.JobInfo)` 를 통해 입력할수 있습니다. 
Job 의 기준이 충족하게 되면 시스템은 이 Job 을 어플리케이션의 `android.app.job.JobService` 으로 실행합니다.
`android.app.job.JobInfo.Builder#Builder(int,android.content.ComponentName)` 으로 JobInfo 를 구성하면 job 과 구현 로직들은 서비스 컴포넌트로 구분할수 있습니다.

framework 는 job 을 실행할때 가능한 연기시키고 모아서 실행하는 방식으로 지능적인 방법을 사용합니다.
일반적으로 deadline 을 정의하지 않으면 JobScheduler 의 내부 queue 의 상태에 따라 어느 시점에든 실행될수 있습니다.

Android version android.os.Build.VERSION_CODES#UPSIDE_DOWN_CAKE 에서부터 JobScheduler 는 사용자에게 더 적은 영향을 주기 위해 더 가능한 시스템 자원을 사용할수 있을때까지 지연하는 방식으로 job 실행을 최적화하려고 합니다.
충분한 배터리, idle, 충전, 네트워크 연결 상태 등이 고려 요소입니다.
Job 들은 모든 자원을 얻을수 있는 것처럼 취급되지만 deadline 에 근접했을때는 제한이 덜 엄격해집니다.
자원에 대한 요청들은 이런 변화에 영향을 주지 않습니다.

job 이 실행될때는 system 은 앱을 위해 wakelock 을 설정합니다.
이런 이유로 job 을 실행하는 동안 시스템이 깨어 있는 것을 보장할수 있습니다.

안드로이드 버전에 따라 실행할수 있는 Job 의 개수는 제한되어 있습니다.
또한 동작시간 제한도 다릅니다.
