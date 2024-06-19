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
