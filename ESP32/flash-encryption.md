* https://docs.espressif.com/projects/esp-idf/en/latest/esp32/security/flash-encryption.html

# ESP32 Flash encryption

ESP32 frash 암호화 기능에 대한 quick start guide 이다.
예제 코드들을 통해 양산/개발 과정에서 어떻게 테스트하고 검증하는지 알수 있다.

## Introduction

Flash 암호화는 ESP32 칩 외부 frash 메모리의 내용을 암호화 하기 위해 만들어졌다.
먼저 F/W 는 암호화되지 않은 상태로 저장되고, 이후 데이터가 최초 부팅과정에서 암호화 된다.
마지막으로, 물리적인 flash redout 기능을 통해 flash 내용을 복구할수 없게 만드는 것이다.

Flash 암호화가 활성화 되면 아래의 데이터가 기본적으로 암호화 된다.

* F/W 부트로더
* Partition Table
* 모든 "app" 타입 파티션들

다른 타입의 데이터는 상태에 따라 암호화 된다:

* `encrypted` flag 가 설정된 partition table 의 모든 파티션들
* 만약 Secure Boot 가 활성화 되었다면 Secure Boot Digest 가 암호화된다.

`Secure Boot` 는 더 안전한 환경을 만들기 위해 flash 암호화와 동시에 사용할수 있는 별도의 기능이다.

> 양산 단계에서는 flash 암호화를 반드시 "Release" 모드로 사용해야 한다.

> flash 암호화를 사용하면 ESP32 의 추가적인 업데이트 옵션이 제한된다. flash 암호화를 사용하기전에 문서를 읽고 구현을 확실히 이해해야 한다.

## 관련 eFuse

flash 암호화 동작은 다양한 ESP32 의 eFuse 들도 제어된다.
eFuse 의 목록과 동작이 아래 테이블에 정리되어 있다.
eFuse 열의 이름들은 espepuse.py 에서도 동일하게 사용된다.
eFuse API 를 사용하기 위해서는 `ESP_EFUSE_` 를 붙여서 사용해야 한다.
예) `espefuse.py esp_efuse_read_field_bit ESP_EFUSE_DISABLE_DL_ENCRYPT`

| eFuse | Description | Bit Depth |
| :--- | :--- | :--- |
| `CODING_SCHEME` | Controls actual number of block1 bits used to derive 마지막 256-bit AES key 를 얻기 위해 사용되는 block1 비트의 실제 번호를 제어한다. `0` 일때 256 bits 를, `1` 이면 192 bits, `2` 이면 128 bits 를 의미한다. 마지막 AES key 는 `FLASH_CRYPT_CONFIG` 에 따라 얻을 수 있다. | 2
| `flash_encryption` (block1) | AES key 저장소 |  256 bit key block
| `FLASH_CRYPT_CONFIG` | AES 암호화 과정 제어 | 4
| `DISABLE_DL_ENCRYPT` | 설정되면 F/W 다운로드 모드 동작중에 flash 암호화가 동작하지 않는다. | 1
| `DISABLE_DL_DECRYPT` | 설정되면 UART F/W 다운로드 모드 동작중에 flash 해독이 동작하지 않는다. | 1 
| `FLASH_CRYPT_CNT` | 부팅시의 암호화 동작 여부를 설정한다. 만약 짝수로 설정되면 부팅 과정에서 flash 를 암호화 한다. 반대로 홀수이면 부팅 과정에서 암호화 동작을 수행하지 않는다. | 7

> * R/W 접근 제어는 위 테이블의 eFuse 비트만으로 충분히 제어 할수 있다.
> * 위 비트들의 공장 출하 설정은 모두 0 이다.

eFuse 비트에 대한 읽기 및 쓰기 접근은 레지스터 `WR_DIS` 및 `RD_DIS` 의 해당 필드에 의해 제어됩니다.
ESP32 eFuse에 대한 자세한 내용은 eFuse 관리자를 참조하십시오.
espefuse.py 를 사용하여 eFuse 필드의 보호 비트를 변경하려면 read_protect_efuse 및 write_protect_efuse 두 명령을 사용하십시오.
예) `espefuse.py write_protect_efuse DISABLE_DL_ENCRYPT.`

## Flash 암호화 과정

eFuse 값이 기본 상태이고 펌웨어 부트로더가 플래시 암호화를 지원하도록 컴파일되었다면, 플래시 암호화 프로세스는 아래의 과정으로 실행됩니다.:

1. 첫 번째 power-on reset 에서 플래시의 모든 데이터는 암호화되지 않습니다(일반 텍스트).
ROM 부트로더는 펌웨어 부트로더를 로드합니다.

2. 펌웨어 부트로더는 `FLASH_CRYPT_CNT` eFuse 값(`0b0000000`)을 읽습니다.
값이 `0`(짝수 비트 설정)이면 플래시 암호화 블록을 구성하고 활성화하며,
`FLASH_CRYPT_CONFIG` eFuse 이 0xF 로 설정합니다.
플래시 암호화 블록에 대한 자세한 내용은 ESP32 기술 참조 설명서 > eFuse 컨트롤러(eFuse) > 플래시 암호화 블록[PDF]을 참조하십시오.

3. 펌웨어 부트로더는 RNG(무작위) 모듈을 사용하여 AES-256 비트 키를 생성한 다음 이를 `flash_encryption` eFuse 에 기록합니다.
`flash_encryption` eFuse에 대한 쓰기 및 읽기 보호 비트가 설정되면 소프트웨어를 통해 비트 키에 접근 할 수 없습니다.
플래시 암호화 작업은 완전히 하드웨어에서만 동작되며 소프트웨어로 키에 접근 할 수 없습니다.

4. 플래시 암호화 블록은 펌웨어 부트로더, 애플리케이션 및 `encrypted` 로 표시된 파티션으로 구성된 플래시의 데이터를 암호화합니다.
내부 암호화는 큰 파티션의 경우 최대 1분까지 시간이 걸릴 수 있습니다.

5. 펌웨어 부트로더는 `FLASH_CRYPT_CNT`(`0b0000001`)에서 사용 가능한 첫 번째 비트를 설정하여 플래시 데이터를 암호화된 것으로 표시합니다.
이렇게 `FLASH_CRYPT_CNT` 가 홀수 값으로 설정됩니다.

6. 개발 모드 일때, 펌웨어 부트로더는 UART 부트로더가 암호화된 바이너리를 다시 플래시할 수 있도록 eFuse 비트 `DISABLE_DL_DECRYPT` 및 `DISABLE_DL_CACHE` 만을 설정합니다.
`FLASH_CRYPT_CNT` eFuse 비트도 write-protection 되지 않습니다.

7. 양산 모드 일때, 펌웨어 부트로더는 eFuse 비트 `DISABLE_DL_ENCRYPT`, `DISABLE_DL_DECRYPT` 및 `DISABLE_DL_CACHE` 을 모두 1 로 설정해 UART 부트로더가 플래시 내용을 해독하지 못하도록 합니다.
또한 `FLASH_CRYPT_CNT` eFuse 비트를 write-protection 으로 설정합니다.
이 동작을 수정하려면 UART 부트로더 암호화/복호화 활성화를 참조하십시오.

8. 위의 동작 이후 ESP32 가 재부팅 되면서 암호화된 이미지 실행이 시작됩니다. 펌웨어 부트로더는 플래시 해독 블록을 호출하여 플래시의 내용을 해독한 다음 IRAM에 로드합니다.

개발 단계에서는 수정에 따른 플래시 이미지 프로그래밍과 플래시 암호화 자체를 테스트하는 과정이 자주 수행됩니다.
펌웨어 다운로드 모드가 필요한 만큼 암호화되지 않은 새로운 이미지를 로드할 수 있어야 합니다.
하지만 제조 및 양산 단계에서는 보안상의 이유로 펌웨어 다운로드 모드에서 플래시 콘텐츠에 액세스할 수 없도록 해야 합니다.

따라서 개발용과 양산 용의 두 가지 다른 방식으로 플래시 암호화 구성이 만들어 졌습니다.
이러한 구성에 대한 자세한 내용은 이어지니는 플래시 암호화 설정 단원을 참조하십시오.

## Flash 암호화 설정


### Development Mode

During development, you can encrypt flash using either an {IDF_TARGET_NAME} generated key or external host-generated key.


#### Using ESP32 Generated Key

```
idf.py flash monitor
```

```
--- idf_monitor on /dev/cu.SLAB_USBtoUART 115200 ---
--- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---
ets Jun  8 2016 00:22:57

rst:0x1 (POWERON_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
configsip: 0, SPIWP:0xee
clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00
mode:DIO, clock div:2
load:0x3fff0018,len:4
load:0x3fff001c,len:8452
load:0x40078000,len:13608
load:0x40080400,len:6664
entry 0x40080764
I (28) boot: ESP-IDF v4.0-dev-850-gc4447462d-dirty 2nd stage bootloader
I (29) boot: compile time 15:37:14
I (30) boot: Enabling RNG early entropy source...
I (35) boot: SPI Speed      : 40MHz
I (39) boot: SPI Mode       : DIO
I (43) boot: SPI Flash Size : 4MB
I (47) boot: Partition Table:
I (51) boot: ## Label            Usage          Type ST Offset   Length
I (58) boot:  0 nvs              WiFi data        01 02 0000a000 00006000
I (66) boot:  1 phy_init         RF data          01 01 00010000 00001000
I (73) boot:  2 factory          factory app      00 00 00020000 00100000
I (81) boot: End of partition table
I (85) esp_image: segment 0: paddr=0x00020020 vaddr=0x3f400020 size=0x0808c ( 32908) map
I (105) esp_image: segment 1: paddr=0x000280b4 vaddr=0x3ffb0000 size=0x01ea4 (  7844) load
I (109) esp_image: segment 2: paddr=0x00029f60 vaddr=0x40080000 size=0x00400 (  1024) load
0x40080000: _WindowOverflow4 at esp-idf/esp-idf/components/freertos/xtensa_vectors.S:1778

I (114) esp_image: segment 3: paddr=0x0002a368 vaddr=0x40080400 size=0x05ca8 ( 23720) load
I (132) esp_image: segment 4: paddr=0x00030018 vaddr=0x400d0018 size=0x126a8 ( 75432) map
0x400d0018: _flash_cache_start at ??:?

I (159) esp_image: segment 5: paddr=0x000426c8 vaddr=0x400860a8 size=0x01f4c (  8012) load
0x400860a8: prvAddNewTaskToReadyList at esp-idf/esp-idf/components/freertos/tasks.c:4561

I (168) boot: Loaded app from partition at offset 0x20000
I (168) boot: Checking flash encryption...
I (168) flash_encrypt: Generating new flash encryption key...
I (187) flash_encrypt: Read & write protecting new key...
I (187) flash_encrypt: Setting CRYPT_CONFIG efuse to 0xF
W (188) flash_encrypt: Not disabling UART bootloader encryption
I (195) flash_encrypt: Disable UART bootloader decryption...
I (201) flash_encrypt: Disable UART bootloader MMU cache...
I (208) flash_encrypt: Disable JTAG...
I (212) flash_encrypt: Disable ROM BASIC interpreter fallback...
I (219) esp_image: segment 0: paddr=0x00001020 vaddr=0x3fff0018 size=0x00004 (     4)
I (227) esp_image: segment 1: paddr=0x0000102c vaddr=0x3fff001c size=0x02104 (  8452)
I (239) esp_image: segment 2: paddr=0x00003138 vaddr=0x40078000 size=0x03528 ( 13608)
I (249) esp_image: segment 3: paddr=0x00006668 vaddr=0x40080400 size=0x01a08 (  6664)
I (657) esp_image: segment 0: paddr=0x00020020 vaddr=0x3f400020 size=0x0808c ( 32908) map
I (669) esp_image: segment 1: paddr=0x000280b4 vaddr=0x3ffb0000 size=0x01ea4 (  7844)
I (672) esp_image: segment 2: paddr=0x00029f60 vaddr=0x40080000 size=0x00400 (  1024)
0x40080000: _WindowOverflow4 at esp-idf/esp-idf/components/freertos/xtensa_vectors.S:1778

I (676) esp_image: segment 3: paddr=0x0002a368 vaddr=0x40080400 size=0x05ca8 ( 23720)
I (692) esp_image: segment 4: paddr=0x00030018 vaddr=0x400d0018 size=0x126a8 ( 75432) map
0x400d0018: _flash_cache_start at ??:?

I (719) esp_image: segment 5: paddr=0x000426c8 vaddr=0x400860a8 size=0x01f4c (  8012)
0x400860a8: prvAddNewTaskToReadyList at esp-idf/esp-idf/components/freertos/tasks.c:4561

I (722) flash_encrypt: Encrypting partition 2 at offset 0x20000...
I (13229) flash_encrypt: Flash encryption completed
I (13229) boot: Resetting with flash encryption enabled...
```

```
rst:0x1 (POWERON_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
configsip: 0, SPIWP:0xee
clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00
mode:DIO, clock div:2
load:0x3fff0018,len:4
load:0x3fff001c,len:8452
load:0x40078000,len:13652
ho 0 tail 12 room 4
load:0x40080400,len:6664
entry 0x40080764
I (30) boot: ESP-IDF v4.0-dev-850-gc4447462d-dirty 2nd stage bootloader
I (30) boot: compile time 16:32:53
I (31) boot: Enabling RNG early entropy source...
I (37) boot: SPI Speed      : 40MHz
I (41) boot: SPI Mode       : DIO
I (45) boot: SPI Flash Size : 4MB
I (49) boot: Partition Table:
I (52) boot: ## Label            Usage          Type ST Offset   Length
I (60) boot:  0 nvs              WiFi data        01 02 0000a000 00006000
I (67) boot:  1 phy_init         RF data          01 01 00010000 00001000
I (75) boot:  2 factory          factory app      00 00 00020000 00100000
I (82) boot: End of partition table
I (86) esp_image: segment 0: paddr=0x00020020 vaddr=0x3f400020 size=0x0808c ( 32908) map
I (107) esp_image: segment 1: paddr=0x000280b4 vaddr=0x3ffb0000 size=0x01ea4 (  7844) load
I (111) esp_image: segment 2: paddr=0x00029f60 vaddr=0x40080000 size=0x00400 (  1024) load
0x40080000: _WindowOverflow4 at esp-idf/esp-idf/components/freertos/xtensa_vectors.S:1778

I (116) esp_image: segment 3: paddr=0x0002a368 vaddr=0x40080400 size=0x05ca8 ( 23720) load
I (134) esp_image: segment 4: paddr=0x00030018 vaddr=0x400d0018 size=0x126a8 ( 75432) map
0x400d0018: _flash_cache_start at ??:?

I (162) esp_image: segment 5: paddr=0x000426c8 vaddr=0x400860a8 size=0x01f4c (  8012) load
0x400860a8: prvAddNewTaskToReadyList at esp-idf/esp-idf/components/freertos/tasks.c:4561

I (171) boot: Loaded app from partition at offset 0x20000
I (171) boot: Checking flash encryption...
I (171) flash_encrypt: flash encryption is enabled (3 plaintext flashes left)
I (178) boot: Disabling RNG early entropy source...
I (184) cpu_start: Pro cpu up.
I (188) cpu_start: Application information:
I (193) cpu_start: Project name:     flash-encryption
I (198) cpu_start: App version:      v4.0-dev-850-gc4447462d-dirty
I (205) cpu_start: Compile time:     Jun 17 2019 16:32:52
I (211) cpu_start: ELF file SHA256:  8770c886bdf561a7...
I (217) cpu_start: ESP-IDF:          v4.0-dev-850-gc4447462d-dirty
I (224) cpu_start: Starting app cpu, entry point is 0x40080e4c
0x40080e4c: call_start_cpu1 at esp-idf/esp-idf/components/esp32/cpu_start.c:265

I (0) cpu_start: App cpu up.
I (235) heap_init: Initializing. RAM available for dynamic allocation:
I (241) heap_init: At 3FFAE6E0 len 00001920 (6 KiB): DRAM
I (247) heap_init: At 3FFB2EC8 len 0002D138 (180 KiB): DRAM
I (254) heap_init: At 3FFE0440 len 00003AE0 (14 KiB): D/IRAM
I (260) heap_init: At 3FFE4350 len 0001BCB0 (111 KiB): D/IRAM
I (266) heap_init: At 40087FF4 len 0001800C (96 KiB): IRAM
I (273) cpu_start: Pro cpu start user code
I (291) cpu_start: Starting scheduler on PRO CPU.
I (0) cpu_start: Starting scheduler on APP CPU.

Sample program to check Flash Encryption
This is ESP32 chip with 2 CPU cores, WiFi/BT/BLE, silicon revision 1, 4MB external flash
Flash encryption feature is enabled
Flash encryption mode is DEVELOPMENT
Flash in encrypted mode with flash_crypt_cnt = 1
Halting...
```


#### Using Host Generated Key



#### Re-flashing Updated Partitions
```bash
idf.py encrypted-app-flash monitor
```

```bash
idf.py encrypted-flash monitor
```

### Release Mode
```bash
idf.py flash monitor
```

#### Best Practices

## Possible Failures


```bash
rst:0x3 (SW_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
flash read err, 1000
ets_main.c 371
ets Jun  8 2016 00:22:57

rst:0x7 (TG0WDT_SYS_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
flash read err, 1000
ets_main.c 371
ets Jun  8 2016 00:22:57

rst:0x7 (TG0WDT_SYS_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
flash read err, 1000
ets_main.c 371
ets Jun  8 2016 00:22:57

rst:0x7 (TG0WDT_SYS_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
flash read err, 1000
ets_main.c 371
ets Jun  8 2016 00:22:57

rst:0x7 (TG0WDT_SYS_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
flash read err, 1000
ets_main.c 371
ets Jun  8 2016 00:22:57
```

```bash
rst:0x3 (SW_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
configsip: 0, SPIWP:0xee
clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00
mode:DIO, clock div:2
load:0x3fff0018,len:4
load:0x3fff001c,len:10464
ho 0 tail 12 room 4
load:0x40078000,len:19168
load:0x40080400,len:6664
entry 0x40080764
I (60) boot: ESP-IDF v4.0-dev-763-g2c55fae6c-dirty 2nd stage bootloader
I (60) boot: compile time 19:15:54
I (62) boot: Enabling RNG early entropy source...
I (67) boot: SPI Speed      : 40MHz
I (72) boot: SPI Mode       : DIO
I (76) boot: SPI Flash Size : 4MB
E (80) flash_parts: partition 0 invalid magic number 0x94f6
E (86) boot: Failed to verify partition table
E (91) boot: load partition table error!
```

```bash
rst:0x3 (SW_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
configsip: 0, SPIWP:0xee
clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00
mode:DIO, clock div:2
load:0x3fff0018,len:4
load:0x3fff001c,len:8452
load:0x40078000,len:13616
load:0x40080400,len:6664
entry 0x40080764
I (56) boot: ESP-IDF v4.0-dev-850-gc4447462d-dirty 2nd stage bootloader
I (56) boot: compile time 15:37:14
I (58) boot: Enabling RNG early entropy source...
I (64) boot: SPI Speed      : 40MHz
I (68) boot: SPI Mode       : DIO
I (72) boot: SPI Flash Size : 4MB
I (76) boot: Partition Table:
I (79) boot: ## Label            Usage          Type ST Offset   Length
I (87) boot:  0 nvs              WiFi data        01 02 0000a000 00006000
I (94) boot:  1 phy_init         RF data          01 01 00010000 00001000
I (102) boot:  2 factory          factory app      00 00 00020000 00100000
I (109) boot: End of partition table
E (113) esp_image: image at 0x20000 has invalid magic byte
W (120) esp_image: image at 0x20000 has invalid SPI mode 108
W (126) esp_image: image at 0x20000 has invalid SPI size 11
E (132) boot: Factory app partition is not bootable
E (138) boot: No bootable app partitions in the partition table
```

## ESP32 Flash Encryption Status
  ```bash

      espefuse.py -p PORT summary
```

## Reading and Writing Data in Encrypted Flash


### Scope of Flash Encryption

### Reading from Encrypted Flash

### Writing to Encrypted Flash

## Updating Encrypted Flash

### OTA Updates

### Updating Encrypted Flash via Serial

## Disabling Flash Encryption

```bash
espefuse.py burn_efuse {IDF_TARGET_CRYPT_CNT}
```

## Key Points About Flash Encryption

## Limitations of Flash Encryption

## Flash Encryption and Secure Boot

## Advanced Features

### Encrypted Partition Flag

```bash
 # Name,   Type, SubType, Offset,  Size, Flags
 nvs,      data, nvs,     0x9000,  0x6000
 phy_init, data, phy,     0xf000,  0x1000
 factory,  app,  factory, 0x10000, 1M
 secret_data, 0x40, 0x01, 0x20000, 256K, encrypted
```

### Enabling UART Bootloader Encryption/Decryption

```bash
espefuse.py --port PORT burn_efuse DISABLE_DL_DECRYPT
espefuse.py --port PORT write_protect_efuse DISABLE_DL_ENCRYPT
```

### Setting FLASH_CRYPT_CONFIG

### JTAG Debugging

### Manually Encrypting Files
```bash
espsecure.py encrypt_flash_data --keyfile /path/to/key.bin --address 0x10000 --output my-app-ciphertext.bin build/my-app.bin
```

## Technical Details

### Flash Encryption Algorithm
