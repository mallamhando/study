# PendingIntent
- https://developer.android.com/reference/android/app/PendingIntent

Intent 와 target 동작을 수행하기 위한 설명이다. `getActivity(Context, int, Intent, int), getActivities(Context, int, Intent, int), getBroadcast(Context, int, Intent, int), and getService(Context, int, Intent, int)` 으로 이 클래스의 인스턴스가 생성된다. 생성된 객체는 나중에 작성자 대신 설명된 동작을 수행하기 위해, 다른 어플리케이션으로 전달 될수 있다. 

PendingIntent 를 다른 어플리케이션에 전달하는 방식으로, 마치 다른 어플리케이션이 작성자의 동일 id 와 권한 안에서 직접 수행하는 것처럼, 작성자가 정의한 동작을 실행할 권리를 승인한다. 그래서 PendingIntent 를 만들때는 항상 주의해야 한다. 예를 들어, 작성자가 제공하는 기본 Intetnt 들은 반드시 작성자가 소유한 컴포넌트 중에 하나로 이름을 명시하고 있어야 한다. 완전하게 어디로 보내고 다른곳으로 가지 않도록 해야 한다.

PendingIntent 자체는 복귀 되기 위해 쓰이는 원본 데이터를 설명하기위해 시스템에 의해 관리되는 단순한 token  참조자다. 이것은 그것을 소유한 어플리케이션 프로세스가 죽어버리더라도 PendingIntent 자체는 주워지기로 정의된 다른 프로세스에 의해 사용될수 있도록 남아 있는 것을 의미한다. 만약 생성된 어플리케이션이 나중에 다시 동일한 PendingIntent(같은 동작, 같은 Intent action, 데이터, 분류, 컴포넌트, 같은 flags) 를 복구하면, 동일한 token 의 유효한 PendingIntent 를 받게 된다. 따라서 `cancel` 를 이용해 삭제해야 한다.

이런 동작 때문에, 두개의 Intent 가 같은 목적의 PendingIntent 를 복구하기 위해 고려되어야 하는지 아는것은 중요하다. 사람들이 만드는 일반적인 실수는 매번 다른 PendingIntent 를 얻기 위해 단지 extra 내용만 다른 다수의 PendingIntent 를 생성하는 것이다. 이것은 발생하지 않는다. Intent 의 부분은 `Intent.filterEquals` 로 정의된 동일한 Intent 을 찾기위해 쓰인다. 만약 작성자가 `Intent.filterEquals` 로 구분되지 않는 두개의 Intent 객체를 사용한다면 작성자는 동일한 PendingIntent 를 받게된다.

이와같은 상황을 다루기 위해 보통 두가지 방법이 쓰인다.

만약 작성자가 정말 구분되는 PendingIntent 를 동시에 사용해야 한다면(두개의 다른 noti 를 동시에 표시하기 위해), 작성자는 반드시 다른 PendingIntent 를 만들기 위해 확실해 해야 할것들이 있다. 그것은 곧 `Intent.filterEquals` 에서 구분 되는 것들인데, `getActivity(Context, int, Intent, int), getActivities(Context, int, Intent, int), getBroadcast(Context, int, Intent, int), and getService(Context, int, Intent, int)` 에 다른 request code 를 입력하는 것이다.

만약 하나의 PendingIntent 만 한번에 필요하다면, `FLAG_CANCEL_CURRENT` 또는 `FLAG_UPDATE_CURRENT` 를 취소하고 수정하기 위해 사용하면 된다. 어떤 상황이든 현재의 PendingIntent 가 원하는 형태로 설정된다.

또한 `FLAG_ONE_SHOT` 또는 `FLAG_IMMUTABLE` 같은 설정은 PendingIntent 를 복구할때 다른 flag 들과 같이 해야 한다. 예를 들어 `FLAG_ONE_SHOT` 으로 된 PendingIntent 를 검색하려면 `FLAG_ONE_SHOT` 과 `FLAG_NO_CREATE` 둘 다를 포함해야 한다.
