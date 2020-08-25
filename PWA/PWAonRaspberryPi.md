# PWA on RaspberryPi
* https://packages.debian.org/search?keywords=chromium
* https://developers.google.com/web/fundamentals/codelabs/your-first-pwapp?hl=ko

PWA 는 Chrome 74 이상에서 설치된다. Raspbian 9 까지는 chrome 72, Raspbian 10 (buster) 는 chrome 83 까지 지원

## Buster(Raspbian 10) 테스트
* [x] 기본 PWA https://googlechromelabs.github.io/text-editor 설치 및 동작
* [x] offline 동작
* [x] 부팅시 자동 실행
  * PWA 설치 후 생성된 바탕화면에 있는 desktop 파일을 `/etc/xdg/autostart/` 으로 복사
* [x] 부팅시 전체 화면으로 실행
* [x] node.js 에서 실행
  * desktop 파일을 직접 호출할 필요없이 `chromium-browser --kiosk --profile-directory=Default --app-id=bhdcongedggceagdbikdldfiflbcbiid` 와 같은 앱실행 명령어를 직접 실행
* [x] 원하는 크기로 원하는 위치에서 실행
  * ~`xdotool`~
  * `window.moveTo`
  * `window.resizeTo`
* [x] windows frame 제거
  * ~`window.toolbar.visible`~
  * application 전체 제거
    * `/etc/xdg/openbox/lxde-pi-rc.xml` 에 `<application class='*'><decor>no</decor></application>` 추가
  * windows, ubuntu 에서의 동일 동작 방법
    * https://developer.mozilla.org/en-US/docs/Web/Manifest/display
    * ubuntu 에서 `maximus` 프로그램을 사용해도 devTool 의 frame 은 제거되지만 PWA 의 Frame 은 제거되지 않음
    * cmd 또는 bash 에서 `chrome --app=URL` 로 실행하면 다른 형태의 title bar 로 구성되지만 제거되지는 않음
    * 현시점에서 불가능, title bar 를 제어하기 위한 새로운 manifest spec 을 MS 에서 제안
      * https://github.com/MicrosoftEdge/MSEdgeExplainers/blob/master/TitleBarCustomization/explainer.md
      * https://web.dev/app-like-pwas/#customized-title-bar
      * https://askubuntu.com/questions/906424/remove-decoration-of-single-window-in-gnome-3
    * electron + iframe 으로 PWA 동작 → offline 에서 정상동작 확인
* [x] windows frame 없는 상태에서 종료
  * `window.close()`
* [x] sdcard 복사 후 offline 실행
* [x] multi 실행
* [x] sdcard 복사 후 app id 동일?
  * `chromium-browser --kiosk --profile-directory=Default --app=https://googlechromelabs.github.io/text-editor` 으로 실행
  * app-id 불필요
* [x] url 에 경로와 query 추가하여 실행
  * main 경로를 처음 접속하지 않으면 sub 경로로 바로 시작할수 없음
