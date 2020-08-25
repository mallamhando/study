---
id: making-a-progressive-web-app
title: Making a Progressive Web App
---

[원문](https://github.com/facebook/create-react-app/blob/master/docusaurus/docs/making-a-progressive-web-app.md)

프로덕션 빌드에는 [Progressive Web App](https://developers.google.com/web/progressive-web-apps/) 의
첫번째 객체를 생성하기 위한 모든 툴을 포함하고 있습니다. 하지만 **offline/cache-first 동작은 opt-in(옵션으로 추가) 에만** 포함되어 있습니다.
기본으로, 빌드 동작은 service worker file 을 생성하지만 등록 과정을 수행하지는 않습니다.

offline-first 을 위한 opt-in 을 위해서, [`src/index.js`](https://github.com/facebook/create-react-app/blob/master/packages/cra-template/template/src/index.js) 파일을 우선 살펴봐야 합니다.

```js
// app 을 offline 에서 동작하고 로딩을 빠르게 하기 위해서는
// unregister() 를 register() 로 바꿔야 합니다.
// 약간의 pitfall 이 수반된다는 것을 인지하십시요.
// service worker 에 대한 더 많은 정보는
// https://cra.link/PWA 에 있습니다.
serviceWorker.unregister();
```

주석과 같이 `serviceWorker.unregister()` 를 `serviceWorker.register()` 로 바꾸면 service worker 를 사용할 수 있게 됩니다.

## 왜 Opt-in 인가?

> Opt-in 은 built-in 처럼 기본 장착된 기능이지만 option 으로 사용 / 미사용이 결정될수 있는 것을 말한다.

Offline-first Progressive Web App 은 기존 웹 페이지들보다 더 빠르고 더 안정적이며, 매력적인 모바일 경험을 제공합니다.

- 보든 정적 사이트 asset 들은 cache 되기 때문에 같은 페이지를 다시 방문했을 때의 네트워크 연결 상태(2G 또는 3G)와 상관없이 속도가 빨라집니다. 또한 업데이트 할 내용들는 background 로 다운로드 됩니다.
- 앱이 네트워크 상태, 심지어 offline 상태에서도 동작합니다. 사용자는 10,000 feet 높이에서도 지하철 속에서도 사용할수 있습니다.
- 모바일 기기에서, 앱은 home screen 에 icon 형태로 바로 더해질수 있습니다. app store 가 필요없습니다.

[can make debugging deployments more challenging](https://github.com/facebook/create-react-app/issues/2398) 와 같은 논쟁이 있었지만, 어쨌든 Create React App 2 로 시작합시다. service worker 를 기본 탑재(Opt-in)합니다.

프로덕션 설정에 포함된 [`workbox-webpack-plugin`](https://developers.google.com/web/tools/workbox/modules/workbox-webpack-plugin) 은 service worker 파일을 자동으로 생성합니다.
service worker 는 모든 local asset 들을 미리 cache 하고, 배포된 가장 최신 버전으로 업데이트 합니다.
service worker 는 [navigation requests](https://developers.google.com/web/fundamentals/primers/service-workers/high-performance-loading#first_what_are_navigation_requests)
과
local asset 들을 제어하기 위해
[cache-first strategy](https://developers.google.com/web/fundamentals/instant-and-offline/offline-cookbook/#cache-falling-back-to-network) 를 사용해서 웹 앱을 안정적이지 않은 네트워크 상황에서도 항상 빠르게 동작할수 있도록 합니다.

## Offline-First 고려 사항들

service worker 등록(register)에 동의했다면, 아래의 항목들을 고려해야 합니다.

1. 첫번째 caching 이 완료된 다음에는, [service worker 생명주기](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)가 사용자에게 보여줄 업데이트된 내용들을 제어합니다.
   [느리게 로딩되는 내용에서 발생하는 경쟁 상태](https://github.com/facebook/create-react-app/issues/3613#issuecomment-353467430) 를 방지하기 위해
   업데이트된 service worker 가 "[waiting](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#waiting)"
   상태를 유지하는 것이 기본 동작입니다.
   따라서, 사용자들은 service worker 가 모든 내용들을 다 로딩하기 전까지 이전 버젼의 내용들을 보게됩니다.
   [블로그 링크](https://jeffy.info/2018/10/10/sw-in-c-r-a.html) 에 더 자세한 내용이 나와있습니다.

1. 사용자들은 항상 offline-first 웹 앱에 익숙하지 않습니다.
   service worker 가 caching 을 완료한 뒤에 사용자들에게 "이 웹은 offline 에서 동작합니다." 같은 메시지를 표시하는 것은 유용한 방법입니다.
   또한, "열었다가 닫혔던 tab 에서 새로운 콘텐츠가 가 업데이트 되었습니다." 같은 메시지를 띄우는 것도 좋은 방법입니다.
   현재 이런 메시지들은 개발자의 선택으로 남겨져 있습니다.
   service worker 소스 [`src/serviceWorker.js`](https://github.com/facebook/create-react-app/blob/master/packages/cra-template/template/src/serviceWorker.js)
   와 console.log 에 표시되는 내용들을 참조할수 있습니다.

1. Service worker 은 [HTTPS 가 필요](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers#you_need_https)하고
   [Local 테스트](https://stackoverflow.com/questions/34160509/options-for-testing-service-workers-via-http/34161385#34161385)가 불가능한것은 아니지만
   정책적으로 사용하지 않는 것이 좋습니다.

1. Service worker 는 `npm run build` 같은 명령어로 실행된 production 빌드 결과물에서만 동작합니다.
   development 환경설정에서는 offline-first servcie worker 를 사용하지 않는것을 권장합니다.
   이전에 cache 된 asset 들이 사용되면 새롭게 수정된 내용들과 충돌을 일으킬수 있기 때문입니다.

1. 그럼에도 불구하고 정말, 진짜, 진심으로 offline-first service worker 를 local 에서 꼭, 반드시 테스트 해야겠다면,
   앱을 먼저 build (`npm run build` 사용) 하고 build 디렉토리의 내용을 http server 로 동작시키는 방법을 사용해야 합니다.
   빌드 script 다음에 CRA 는 production 결과물을 어떻게 테스트 할수 있는지 표시할 겁니다.
   아니면 deploy 사이트를 참조하는 방법도 있습니다.
   브라우더 캐쉬 충돌을 방지하기 위해 항상 익명모드(private mode)로 테스트 하십시요.

1. 기본적으로, 생성된 service worker 파일은 
   HTTP 이미지들이나 다른 도메인에서 로딩된 영상 등의 cross-origin 한 데이터를 cache 하거나 intercept 하지 않을 것입니다.

## Progressive Web App Metadata

웹앱을 표시하기 위한 기본 값들은 `public/manifest.json` 경로에 설정되어 있습니다.
사용자가 웹앱을 바탕화면에 설치하면 나타나는 아이콘, 이름 및 앱을 사용할 때 사용할 브랜드 색을 `manifest.json` 파일로 설정할 수 있습니다.

[The Web App Manifest guide](https://developers.google.com/web/fundamentals/engage-and-retain/web-app-manifest/) 에 어떻게 설정하는지에 대한 자세한 방법이 나와있으니 참조하십시요.

웹앱 manifest 정보는 service worker 가 없는 상황에서도 사용됩니다.
