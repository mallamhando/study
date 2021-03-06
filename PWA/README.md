# PWA
### BT
* 현재는 BLE 만 지원
* https://googlechrome.github.io/samples/web-bluetooth/

### WiFi
* https://developer.mozilla.org/en-US/docs/Web/API/Network_Information_API

### Data Store
##### https://dexie.org/docs/Version/Version.stores()
Never index properties containing images, movies or large (huge) strings. Store them in IndexedDB, yes! but just don’t index them!
```
db.version(1).stores({
  friends: '++id, name, age' // don't index "picture"
});

db.friends.put({
  name: 'Camilla',
  age: 25,
  picture: await getBlob('camilla.png') // but store it
});
```
Example how the “picture” property is stored without being indexed.

Writing this because there have been some issues on github where people index images or movies without really understanding the purpose of indexing fields. A rule of thumb: Are you going to put your property in a where(‘…’) clause? If yes, index it, if not, dont. Large indexes will affect database performance and in extreme cases make it unstable.

##### https://www.raymondcamden.com/2015/04/17/indexeddb-and-limits
* Chrome 은 데이터 공간이 모자르면 LRU 정책에 따라 IndexedDB 를 임의로 삭제할수 있다.
##### https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Browser_storage_limits_and_eviction_criteria

##### https://web.dev/storage-for-the-web/
* Chrome 에서는 디스크의 60 % 까지 사용 가능
* Chrome 은 임의로 지우지 않는다. 어느정도 안전하다.
* cache 삭제 등 local data 전체 삭제 기능을 사용하면 같이 지워질수 있다.
* iOS 계열의 browser 는 사용한지 7 일이 넘는 데이터는 자동으로 삭제한다. 하지만 PWA 로 설치된 앱은 예외다.

##### https://github.com/dfahlander/Dexie.js/issues/577
* blob 데이터는 저장 가능하지만 indexed 되거나 key 값으로 사용할 수 없다.

##### https://web.dev/native-file-system/
* 아직 표준화 작업중인 기능
* local 파일을 직접 읽고 수정할수 있다.
* https://chromium.googlesource.com/chromium/src/+/lkgr/docs/security/permissions-for-powerful-web-platform-features.md
* 설치된 site 는 읽기 쓰기 권한이 유지된다고 하지만 예제 site 에서는 해당 기능이 동작되지는 않았음

##### https://developer.mozilla.org/ko/docs/Web/Progressive_web_apps/App_structure
* 앱 쉘 개념
* SSR 에 PWA 를 적용하기

### Install
##### HTTPS 필수
* HTTPS 접속된 PWA 만 설치가 가능하다.
* local server 의 SSR 의 경우 별도의 HTTPS 작업이 필요하다.
* offline 상태에서의 설치가 어렵다.
* https://create-react-app.dev/docs/using-https-in-development/#windows-cmdexe
```
set HTTPS=true&&npm start   // Windows CMD

($env:HTTPS = "true") -and (npm start)  // Windows PowerShell
```
* local 에서 HTTPS 로 동작시킬수 있지만 SSL 인증오류가 발생하기 때문에 설치는 안된다.
* github page 등을 통해 배포하면 https 경로로 앱을 동작시킬수 있고 설치가 가능하다.

### CRA-making-a-progressive-web-app
https://github.com/mallamhando/study/blob/master/making-a-progressive-web-app-kr.md

### CRA service worker
CRA service worker 코드는 자동으로 생성된다.
자동으로 생성된 코드는 google 의 workbox 를 기반으로 동작한다.
내용적으로 보이는 것은 CRA 가 생성한 js, css 파일들을 precache 하는것이 전부다.

사용자에게 제공된 코드는 service worker 를 등록하고 해제하는 것뿐이다.

### vs Flutter
PC 게임 계속해서 진보된 성능을 보여주는 데에도 콘솔 게임은 지속적으로 자신의 영역을 유지하며 발전하고 있다.
native app 이 계속해서 필요한 이유와 비슷하다.
Flutter 는 PWA 보다 더 native 에 가까운 성능을 보여준다.
사용자 경험을 극대화하고 싶다면 Flutter 또는 기존 native app 을 사용해야 하고, 생산성과 효율성이 중요하다면 PWA 가 유리할 것으로 보인다.

##### Flutter Reference
* https://engineering.linecorp.com/ko/blog/flutter-pros-and-cons/
* https://brunch.co.kr/@tilltue/56
