# WebUSB, WebSerial
## 결론
* https://bugs.chromium.org/p/chromium/issues/detail?id=1119497&q=component%3ABlink%3ESerial%20guid&can=2

Windows 의 역사 만큼 Serial Driver 의 역사도 오래되었다.
필요에 따라 더해진 기능이 오래된 Serial Driver 에는 적용되지 않고 있다.

Google Chrome 에서는 오래된 방식의 Serial Driver 를 감지하는데 어려움을 겪고 있다.
오래된 방식으로는 Chrome 이 실행되기전의 Device 는 인식하지만 실행 이후 연결된 Device 를 인식하지 못한다.
Serial 의 동작 방식과, USB 의 Plug and Play 컨셉이 다르기 때문이다.

Issue 로그에는 flag 설정으로 오래된 방식의 Serial Driver 를 인식할수 있다고 하지만,
실제로 그렇지 않은것으로 보인다.

## 자료
### ESP cp2102 WebUSB
* https://gist.github.com/FeedDahBirds/98301084ce13898fa72c610f211e1e76
* https://gist.github.com/nuta/2c70ba8855f50c536a51f0c5993c1e4c
  * FeedDahBirds 보다 개선된 버젼으로 보인다.
  * WebSerial 을 사용하라고 권장하고 있다.

### microbit WebUSB
* https://support.microbit.org/support/solutions/articles/19000105428-webusb-troubleshooting

### WebSerial Example
* https://github.com/svendahlstrand/web-serial-api/blob/master/src/dimmer.html

### Chrome WebSerial Example
* https://googlechromelabs.github.io/serial-terminal/

### Permission 확인 사이트
* 각종 device 에 대한 동작을 확인할수 있다.
* https://permission.site/

## Windows 에서 인식 안됨 오류
### winusb 를 이용한 방법
* webserial 과 다를것으로 예상
* https://github.com/webusb/arduino/issues/4

### chrome show only GUID_DEVINTERFACE_COMPORT
* https://bugs.chromium.org/p/chromium/issues/detail?id=884928#c128
* https://chromium-review.googlesource.com/c/chromium/src/+/2099238
* https://rageworx.pe.kr/1589
  * chrome 은 GUID_DEVINTERFACE_COMPORT 인 port 만 인식한다.
  * CP210x driver 는 GUID_DEVINTERFACE_SERENUM_BUS_ENUMERATOR 을 사용하기 때문에 인식이 안되고 있다.

### MS VirtualSerial Sample
https://github.com/Microsoft/Windows-driver-samples/tree/master/serial/VirtualSerial

### Polyfill for CP210x
https://github.com/google/web-serial-polyfill/issues/9
CP210x 가 WebUSB 로만 인식 가능하기 때문에 polyfill 를 통해 Serial 로 사용할수 있는것으로 보인다.

### WebUSB for cp210x
* cp210x 가 WebUSB 로 인식되는것도 아니다. Microbit USB 는 정상적으로 인식된다. 둘사이의 차이점을 확인해야 한다.

#### windows 10 에서 WebUSB 인식 성공
* virtualBox 가 아닌 windows 10 에서 permission.site 으로 WebUSB 인식 성공
* 사용자의 windows 7 에서 permission.site 으로 WebUSB 인식 성공
  * `USB2.0-CRW - 페이링됨` 이고 CP210x USB bridge 는 페이링 안뜸

### WebUSB bug report
https://bugs.chromium.org/p/chromium/issues/list?q=component:Blink%3EUSB

### Chrome WebUSB Status
https://www.chromestatus.com/feature/5651917954875392

### Chrome WebSerial Bug report
https://bugs.chromium.org/p/chromium/issues/detail?id=1015074&q=component%3ABlink%3ESerial&can=2

## WebUSB 를 이용한 읽기
### 자료
* https://gist.github.com/nuta/2c70ba8855f50c536a51f0c5993c1e4c
#### Linux USB unbind
* https://lwn.net/Articles/143397/

### Repository
https://github.com/enthusapp/WebSerialTest

### 테스트 1 https://github.com/enthusapp/WebSerialTest/commit/7c7d66ee15bf79159aa4b737e5f097d2a1149645
* `dmesg` 로 cp210x 의 USB ID 를 확인한다.
```
[27704.088578] cp210x 5-2:1.0: cp210x converter detected
```
* `su` 로 진입해서 unbind 한다.
```
sudo su
echo -n "5-2:1.0" > /sys/bus/usb/drivers/cp210x/unbind
```
* USBDevice.claimInterface() 에러가 발생하지 않는다.
* 웹 페이지가 닫히면 다시 usb 가 bind 된다.
* tab 을 닫지 않은 상태로 유지하면 usb 가 다시 bind 되지 않는다.

### 테스트 2 https://github.com/enthusapp/WebSerialTest/commit/86a69f5606252a663d5181151e8c4e1ebbfddf7f
* read 결과가 0 으로만 출력된다. 원인 파악 필요
* read 반복후 정상 데이터 수신

### 테스트 3
* windows 에서 access denied 발생
* https://stackoverflow.com/questions/47143148/webusb-access-denied-trying-to-open-printer-on-windows
* https://stackoverflow.com/questions/46179569/smartcard-reader-access-denied-while-claiming-interface-with-webusb-on-chrome/46186495#46186495
* winUSB.sys 가 없으면 chrome 에서 접근 안됨
#### Windows 해결안
* https://stackoverflow.com/questions/64803300/web-usb-devices-not-displaying-in-chrome-windows-10/64812116#64812116
* 기존 캘리브레이션 툴 에러 없는지 확인 필요
#### 테스트 결과
* WebUSB 인식은 하지만 COM Port 인식은 안된다.
* 이전 프로그램들을 동작시킬수 없다.

### WebUSB 결론
* 사용자가 usb 드라이버를 변경해 가면서 작업하기 어려운 점이 있다.
* usb 드라이버를 교체해야 한다면, 교체해서 serial 로 인식시키게 하는것이 더 낫다.

#### SiliconLab usb driver cutsomization
* https://www.silabs.com/documents/public/application-notes/an220-usb-driver-customization.pdf

#### WebUSB 용 드라이버를 만드는 방법
https://web.dev/build-for-webusb/
