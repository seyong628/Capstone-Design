# 🏭 Gesture-Based Smart Factory Digital Twin System

> 손 제스처로 가상 공장 환경을 제어하고, 실제 센서 데이터를 실시간으로 동기화하는 디지털 트윈 시스템

---

## 📌 프로젝트 개요

본 프로젝트는 **MediaPipe 기반 손 제스처 인식**과 **MQTT 통신 프로토콜**을 활용하여 Unity 3D 가상 공장과 실제 하드웨어를 실시간으로 동기화하는 **스마트 팩토리 디지털 트윈 시스템**입니다.

- 온도 센서가 기계 이상을 감지하면 LED가 깜빡이고, Unity 3D 가상 공장에서 해당 기계가 경고 상태로 표시됩니다.
- 사용자는 손 제스처로 가상 공장 내 기계를 선택하여 현재 상태(온도, 이상 여부 등)를 확인할 수 있습니다.

---

## 🧩 시스템 블럭도

```mermaid
flowchart TD
    A["📷 웹캠\n손 제스처 입력"]
    B["🌡️ DHT11 온도 센서 x3\n기계 이상 감지"]
    C["🐍 MediaPipe + Python\n제스처 인식 + 명령 변환"]
    D["⚙️ Arduino Nano RP2040 Connect\n센서 읽기 + LED 제어 + WiFi MQTT"]
    E["📡 MQTT 브로커\nRaspberry Pi 4 / Mosquitto"]
    F["🎮 Unity 3D\n가상 공장 디지털 트윈"]
    G["💡 LED x3\n이상 상태 표시"]
    H["🔄 동기화 상태\n가상 = 현실 항상 일치"]

    subgraph INPUT["입력 계층"]
        A
        B
    end

    subgraph PROCESS["처리 계층"]
        C
        D
    end

    subgraph COMM["통신 계층"]
        E
    end

    subgraph OUTPUT["출력 계층"]
        F
        G
    end

    subgraph SYNC["동기화 계층"]
        H
    end

    A --> C
    B --> D
    C -->|MQTT publish| E
    D -->|WiFi MQTT publish| E
    E -->|MQTT subscribe| F
    E -->|MQTT subscribe| G
    F --> H
    G --> H
```

---

## 🔄 데이터 흐름

### 시나리오 A — 이상 감지
```
DHT11 온도 상승
  → RP2040 LED 깜빡임
  → WiFi MQTT publish (factory/machine/status)
  → Unity 가상 기계 빨간색으로 변환
  → 사용자 제스처로 기계 선택
  → 상태 팝업 표시 (온도: 78°C / 상태: 이상)
```

### 시나리오 B — 정상 제어
```
손 제스처 (Open Hand)
  → MediaPipe 명령 변환
  → MQTT publish (factory/control)
  → Unity 가상 기계 업데이트 (동시)
  → RP2040 하드웨어 반응 (동시)
```

---

## 📡 MQTT 토픽 구조

| 토픽 | 방향 | 설명 |
|------|------|------|
| `factory/machine/1/status` | RP2040 → All | 기계 1 센서 데이터 전송 |
| `factory/machine/2/status` | RP2040 → All | 기계 2 센서 데이터 전송 |
| `factory/machine/3/status` | RP2040 → All | 기계 3 센서 데이터 전송 |
| `factory/machine/alert` | RP2040 → All | 이상 감지 경보 전송 |
| `factory/control` | Python → All | 제스처 명령 전송 |

---

## 🖐️ 제스처 명령

| 제스처 | 동작 |
|--------|------|
| ✋ 손 펴기 (Open Hand) | 기계 선택 / 확인 |
| ✌️ 두 손가락 | 기계 제어 실행 |
| ✊ 주먹 | 취소 / 초기화 |

---

## 🔧 기술 스택

| 분류 | 기술 |
|------|------|
| 제스처 인식 | Python, MediaPipe, OpenCV |
| 통신 | MQTT (paho-mqtt), Mosquitto |
| 중앙 제어 | Raspberry Pi 4 |
| 하드웨어 | Arduino Nano RP2040 Connect (C/C++), DHT11 x3, LED x3 |
| 가상 환경 | Unity 3D (C#) |

---

## 📦 하드웨어 구성

| 장비 | 용도 |
|------|------|
| Raspberry Pi 4 | MQTT 브로커 (Mosquitto) |
| Arduino Nano RP2040 Connect | 센서 읽기 + LED 제어 + WiFi MQTT 통신 |
| DHT11 온도 센서 x3 | 기계 이상 감지 |
| LED x3 | 이상 상태 시각적 표시 |
| 웹캠 (Cosy FullHD) | 손 제스처 입력 |

---

## 🔌 핀 배선

| 부품 | 핀 연결 |
|------|--------|
| DHT11 #1 | (+)→3.3V, (-)→GND, (S)→D2 |
| DHT11 #2 | (+)→3.3V, (-)→GND, (S)→D3 |
| DHT11 #3 | (+)→3.3V, (-)→GND, (S)→D4 |
| LED #1 | D10 → 220Ω → LED+ / LED- → GND |
| LED #2 | D11 → 220Ω → LED+ / LED- → GND |
| LED #3 | D12 → 220Ω → LED+ / LED- → GND |



---

## 🚀 개발 순서

1. **MQTT 통신 구축** — Raspberry Pi 4 Mosquitto 설치 및 테스트
2. **RP2040 구현** — DHT11 x3 + LED x3 + WiFi MQTT publish/subscribe
3. **Python 제스처 인식** — MediaPipe Hands + MQTT publish
4. **Unity 가상 공장** — 3D 환경 구성 + MQTT subscribe + C# 스크립트
5. **전체 연동 테스트** — 동기화 및 지연 시간 측정



---


> 임베디드 소프트웨어학과 졸업작품 | 2025
