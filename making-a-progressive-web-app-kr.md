---
id: making-a-progressive-web-app
title: Making a Progressive Web App
---

[원문](https://github.com/facebook/create-react-app/blob/master/docusaurus/docs/making-a-progressive-web-app.md)

프로덕션 빌드에는 [Progressive Web App](https://developers.google.com/web/progressive-web-apps/) 의
첫번째 객체를 생성하기 위한 모든 툴을 포함하고 있습니다. 하지만 **offline/cache-first 동작은 opt-in 에만** 포함되어 있습니다.
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

Offline-first Progressive Web App 은 기존 웹 페이지들보다 더 빠르고 더 안정적이며, 매력적인 모바일 경험을 제공합니다.

- 보든 정적 사이트 asset 들은 cache 되기 때문에 같은 페이지를 다시 방문했을 때의 네트워크 연결 상태(2G 또는 3G)와 상관없이 속도가 빨라집니다. 또한 업데이트 할 내용들는 background 로 다운로드 됩니다.
- 앱이 네트워크 상태, 심지어 offline 상태에서도 동작합니다. 사용자는 10,000 feet 높이에서도 지하철 속에서도 사용할수 있습니다.
- 모바일 기기에서, 앱은 home screen 에 icon 형태로 바로 더해질수 있습니다. app store 가 필요없습니다.

[can make debugging deployments more challenging](https://github.com/facebook/create-react-app/issues/2398) 와 같은 논쟁이 있었지만, 어쨌든 Create React App 2 로 시작합시다. service worker 는 opt-in 합니다.

프로덕션 설정에 포함된 [`workbox-webpack-plugin`](https://developers.google.com/web/tools/workbox/modules/workbox-webpack-plugin) 은 service worker 파일을 자동으로 생성합니다.
service worker 는 모든 local asset 들을 미리 cache 하고, 배포된 가장 최신 버전으로 업데이트 합니다.
service worker 는 [navigation requests](https://developers.google.com/web/fundamentals/primers/service-workers/high-performance-loading#first_what_are_navigation_requests)
과
local asset 들을 제어하기 위해
[cache-first strategy](https://developers.google.com/web/fundamentals/instant-and-offline/offline-cookbook/#cache-falling-back-to-network) 를 사용해서 웹 앱을 안정적이지 않은 네트워크 상황에서도 항상 빠르게 동작할수 있도록 합니다.

## Offline-First 고려 사항들

If you do decide to opt-in to service worker registration, please take the
following into account:

1. After the initial caching is done, the [service worker lifecycle](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)
   controls when updated content ends up being shown to users. In order to guard against
   [race conditions with lazy-loaded content](https://github.com/facebook/create-react-app/issues/3613#issuecomment-353467430),
   the default behavior is to conservatively keep the updated service worker in the "[waiting](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#waiting)"
   state. This means that users will end up seeing older content until they close (reloading is not
   enough) their existing, open tabs. See [this blog post](https://jeffy.info/2018/10/10/sw-in-c-r-a.html)
   for more details about this behavior.

1. Users aren't always familiar with offline-first web apps. It can be useful to
   [let the user know](https://developers.google.com/web/fundamentals/instant-and-offline/offline-ux#inform_the_user_when_the_app_is_ready_for_offline_consumption)
   when the service worker has finished populating your caches (showing a "This web
   app works offline!" message) and also let them know when the service worker has
   fetched the latest updates that will be available the next time they load the
   page (showing a "New content is available once existing tabs are closed." message). Showing
   these messages is currently left as an exercise to the developer, but as a
   starting point, you can make use of the logic included in [`src/serviceWorker.js`](https://github.com/facebook/create-react-app/blob/master/packages/cra-template/template/src/serviceWorker.js), which
   demonstrates which service worker lifecycle events to listen for to detect each
   scenario, and which as a default, only logs appropriate messages to the
   JavaScript console.

1. Service workers [require HTTPS](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers#you_need_https),
   although to facilitate local testing, that policy
   [does not apply to `localhost`](https://stackoverflow.com/questions/34160509/options-for-testing-service-workers-via-http/34161385#34161385).
   If your production web server does not support HTTPS, then the service worker
   registration will fail, but the rest of your web app will remain functional.

1. The service worker is only enabled in the [production environment](deployment.md),
   e.g. the output of `npm run build`. It's recommended that you do not enable an
   offline-first service worker in a development environment, as it can lead to
   frustration when previously cached assets are used and do not include the latest
   changes you've made locally.

1. If you _need_ to test your offline-first service worker locally, build
   the application (using `npm run build`) and run a standard http server from your
   build directory. After running the build script, `create-react-app` will give
   instructions for one way to test your production build locally and the [deployment instructions](deployment.md) have
   instructions for using other methods. _Be sure to always use an
   incognito window to avoid complications with your browser cache._

1. By default, the generated service worker file will not intercept or cache any
   cross-origin traffic, like HTTP [API requests](integrating-with-an-api-backend.md),
   images, or embeds loaded from a different domain.

## Progressive Web App Metadata

The default configuration includes a web app manifest located at
[`public/manifest.json`](https://github.com/facebook/create-react-app/blob/master/packages/cra-template/template/public/manifest.json), that you can customize with
details specific to your web application.

When a user adds a web app to their homescreen using Chrome or Firefox on
Android, the metadata in [`manifest.json`](https://github.com/facebook/create-react-app/blob/master/packages/cra-template/template/public/manifest.json) determines what
icons, names, and branding colors to use when the web app is displayed.
[The Web App Manifest guide](https://developers.google.com/web/fundamentals/engage-and-retain/web-app-manifest/)
provides more context about what each field means, and how your customizations
will affect your users' experience.

Progressive web apps that have been added to the homescreen will load faster and
work offline when there's an active service worker. That being said, the
metadata from the web app manifest will still be used regardless of whether or
not you opt-in to service worker registration.
