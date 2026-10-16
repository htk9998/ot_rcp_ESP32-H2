# ot_rcp_ESP32-H2 — ESP Thread Border Router RCP 펌웨어

[ESP Thread Border Router 보드](https://docs.espressif.com/projects/esp-thread-br/en/latest/hardware_platforms.html)의
**ESP32-H2(RCP, Radio Co-Processor)** 펌웨어. Thread 스택은 S3 호스트에서 돌고, H2는 Spinel(UART 460800)로
명령을 받아 IEEE 802.15.4 프레임 송수신만 담당한다.

원본: [esp-idf/examples/openthread/ot_rcp](https://github.com/espressif/esp-idf/tree/v5.5.4/examples/openthread/ot_rcp)
(ESP-IDF v5.5.4, `README_upstream.md` 참조). 소스 수정 없음.

## 보드 구성

```
[Thread 기기들] ~~802.15.4~~ [ESP32-H2: 이 펌웨어] ──UART (Spinel)── [ESP32-S3: BR 호스트] ──Wi-Fi── LAN
                                                                     ↑ 호스트 펌웨어 저장소:
                                                                     https://github.com/htk9998/basic_thread_border_router_ESP32-S3
```

| 칩 | 역할 | 저장소 |
|---|---|---|
| ESP32-H2 | RCP — 802.15.4 송수신 전담 | **이 저장소** |
| ESP32-S3 | 호스트 — Thread 스택 + BR 기능 | https://github.com/htk9998/basic_thread_border_router_ESP32-S3 |

## 플래시 방법 — 보통 직접 굽지 않는다

BR 호스트 펌웨어(`AUTO_UPDATE_RCP=y`)가 **이 빌드 산출물을 자기 이미지에 포함**하고, 첫 부팅 때
UART + 리셋/부트 GPIO(S3 GPIO7/8 → H2 EN/GPIO9)로 H2에 자동 플래시한다.
따라서 역할은 "호스트 빌드 **전에** 빌드해 두는 것"이다.

```bash
. ~/esp/esp-idf-v5.5.4/export.sh
cd ~/esp/projects/ot_rcp_ESP32-H2
idf.py set-target esp32h2
idf.py build            # build/ 산출물을 BR 호스트 빌드가 자동으로 집어감
```

직접 굽고 싶을 때(디버깅)는 보드 **USB1(H2 포트)** 에 연결 후:
```bash
idf.py -p /dev/ttyACM1 erase-flash flash
```

## 설정 포인트

- UART 핀/속도: `main/esp_ot_config.h` (`radio_uart_config`) — 공식 보드는 UART0, 460800 기본값 그대로
- 독립 모듈 조합(데브킷 DIY)이면 위 파일의 핀을 보드 결선에 맞게 수정

## 관련 저장소

- BR 호스트 펌웨어: https://github.com/htk9998/basic_thread_border_router_ESP32-S3
- Matter over Thread 테스트 디바이스/문서: https://github.com/htk9998/thread-on-matter-otbr
