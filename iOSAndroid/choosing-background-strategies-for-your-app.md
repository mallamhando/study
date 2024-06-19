# Choosing Background Strategies for Your App
- https://developer.apple.com/documentation/backgroundtasks/choosing-background-strategies-for-your-app

## Overview

만약 앱이 foreground 에서 동작하지 않을때 task 를 완료하기 위해 컴퓨팅 리소스를 사용해야 한다면,
백그라운드 동작시간을 얻기위한 다양한 전략중 하나늘 선택해야 한다.
올바른 전략을 선택하는 것은 앱이 어떤 기능을 백그라운드에서 수행하는 지에 따라 다르다.

어떤 앱은 foreground 에서 짧은 시간 동작하고, 반드시 백그라운드로 가야할때 방해받지 않고 유지를 해야 한다.
