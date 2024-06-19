 # Scheduling a notification locally from your app
- https://developer.apple.com/documentation/usernotifications/scheduling-a-notification-locally-from-your-app

사용자의 주의를 끌기위해 notification 을 생성하고 스케줄을 설정한다.

## Overview

사용자의 주의를 끌기위해 local notification 를 사용한다.
Alert 을 표시하고 소리를 재생하거나 앱아이콘을 표시할수 있다.
예를 들어 백그라운드 앱이 특정 task 를 끝내고 난뒤에 alert 를 표시하는것을 시스템에 요청할수 있다.
local notification 은 항상 사용자가 원하는 중요한 정보를 전달하기 위해 사용해야 한다.

## Create the notification's content

[UNMutableNotificationContent](https://developer.apple.com/documentation/usernotifications/unmutablenotificationcontent) 
객체의 속성을 notification 의 자세한 정보로 채운다.
각 속성은 어떻게 시스템이 앱의 notification 을 전달할지를 정의한다.
예를 들어 소리를 재생하기위해 객체의 소리 속성 값을 정의해야 한다.
List 1 코드는 내용 객체에 alert 을 어떤 제목과 어떤 body 문구로 설정할지를 나타낸다.
이와 같은 방식으로 다양한 형태의 interaction 을 설정할수 았다.

Listing 1. Configuring the notification content

```swift
let content = UNMutableNotificationContent()
content.title = "Weekly Staff Meeting"
content.body = "Every Tuesday at 2pm"
```

## Specify the conditions for delivery

[UNCalendarNotificationTrigger](https://developer.apple.com/documentation/usernotifications/uncalendarnotificationtrigger), 
[UNTimeIntervalNotificationTrigger](https://developer.apple.com/documentation/usernotifications/untimeintervalnotificationtrigger)
 , 
또는 [UNLocationNotificationTrigger](https://developer.apple.com/documentation/usernotifications/unlocationnotificationtrigger)
객체를 이용해 전달 상태를 설정할 수 있다.
각 trigger 객체는 다른 파라미터들이 필요하다.
예를 들어, calendar 기반 trigger 는 전달을 위한 시간과 날짜가 필요하다.

Listing 2 코드는 매주 화요일 오후 2시에 시스템이 notification 을 전달할수 있게 설정하는 예시다.
[DateComponents](https://developer.apple.com/documentation/foundation/datecomponents)
구조체는 이벤트의 시간을 설정한다.
`repeats` 파라미터를 `true` 로 설정하면 시스템은 매일 notification 이벤트 실행 이후에 다시 설정한다.

Listing 2. Configuring a recurring date-based trigger

```swift
// Configure the recurring date.
var dateComponents = DateComponents()
dateComponents.calendar = Calendar.current


dateComponents.weekday = 3  // Tuesday
dateComponents.hour = 14    // 14:00 hours
   
// Create the trigger as a repeating event.    
let trigger = UNCalendarNotificationTrigger(
         dateMatching: dateComponents, repeats: true)
```

## Cancel a scheduled notification request

한번 스케줄 설정되면, notification 요청은 trigger 조건에 도달할때까지 활성화된 상태로 남아있게 된다.
아니라면, 명벽하게 취소해야한다.
일반적으로, 조건을 변경되거나 사용자게 더이상 필요하지 않을때 요청은 취소된다.
예를 들어 사용자가 기억해야 할것을 완료하거나,
같이 해야할 동작 요청들을 취소해야할 때가 있다.
활성화된 notification 요청을 취소하기 위해서는,
[UNUserNotificationCenter](https://developer.apple.com/documentation/usernotifications/unusernotificationcenter) 의 
[removePendingNotificationRequests(withIdentifiers:)](https://developer.apple.com/documentation/usernotifications/unusernotificationcenter/removependingnotificationrequests(withidentifiers:))
 매소드를 사용해야 한다.
