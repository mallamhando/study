# PWA
### BT
* 현재는 BLE 만 지원
* 
https://googlechrome.github.io/samples/web-bluetooth/

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
