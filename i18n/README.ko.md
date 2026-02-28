[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# Raspberry-Pi 자동 Wi‑Fi 액세스 포인트

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)
![Config: NAT Routed AP](https://img.shields.io/badge/Config-NAT%20Routed%20AP-2b6cb0)

Raspberry Pi를 위한 자동화된 WiFi 액세스 포인트 설정 스크립트입니다. `wlan0`를 AP로 구성하고 `eth0`을 통해 클라이언트 트래픽을 NAT로 라우팅합니다.

> 하나의 안내형 스크립트로 신뢰할 수 있는 라우팅형 Wi‑Fi AP를 구축하세요.

## 🧩 스냅샷

| 항목 | 세부사항 |
| --- | --- |
| 프로젝트 범위 | Raspbian/Debian 기반 Raspberry Pi 액세스 포인트 자동화 |
| 기본 AP 서브넷 | `10.20.1.1/24` |
| DHCP 임대 범위 | `10.20.1.5` - `10.20.1.100` |
| Wi‑Fi 라디오 기본값 | `hw_mode=g`, 채널 `2`, 국가 코드 `US` |
| 스크립트가 다루는 파일 | `/etc/dhcpcd.conf`, `/etc/hostapd/hostapd.conf`, `/etc/dnsmasq.conf`, `/etc/sysctl.d/routed-ap.conf` |

---

## 🌐 개요

이 저장소에는 Raspberry Pi를 Wi‑Fi 라우터로 바꿔주는 대화형 셸 스크립트 `setup.sh` 하나가 포함되어 있습니다. 라우팅형 AP에 필요한 패키지와 서비스를 설치·설정합니다.

- `dnsmasq` 기반 **DHCP/DNS**
- `hostapd` 기반 **AP 데몬**
- `iptables`/`netfilter-persistent` 기반 **영구 NAT 및 패킷 포워딩**

스크립트가 생성하는 기본 액세스 포인트 설정:

| 항목 | 값 |
| --- | --- |
| AP 인터페이스 IP | `10.20.1.1/24` |
| DHCP 범위 | `10.20.1.5` - `10.20.1.100` |
| AP 로컬 이름 | `rpi.ap` |
| WiFi 모드 | 2.4 GHz (`hw_mode=g`) |
| 채널 | `2` |
| 국가 코드 | `US` |

## ✨ 기능

- SSID와 WPA2 비밀번호(확인 입력 포함)를 대화형으로 입력받음
- 핵심 의존성 설치:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- `wlan0`의 고정 AP 네트워크를 구성
- `/etc/sysctl.d/routed-ap.conf`에서 IPv4 포워딩 활성화
- `eth0`을 통한 아웃바운드 트래픽에 대한 영구 NAT/마스커레이드 규칙 추가
- `/etc/dnsmasq.conf`와 `/etc/hostapd/hostapd.conf` 자동 생성
- 부팅 시 `hostapd.service` 활성화 및 시작

## 🗂️ 프로젝트 구조

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ 사전 요구사항

- Raspberry Pi OS(또는 호환되는 Debian 기반 이미지)에서 실행되는 Raspberry Pi
- `wlan0`으로 노출되는 Wi‑Fi 어댑터
- `eth0`의 이더넷 업링크
- 패키지 설치를 위한 인터넷 연결
- `sudo` 권한

전제 조건: 스크립트 동작은 기본 인터페이스명(`wlan0` 및 `eth0`)을 기준으로 작성되었습니다. 표준이 아닌 이름을 사용할 경우 설치 후 수동 조정이 필요합니다.

## 📦 설치

```bash
# 저장소 클론
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
sudo chmod +x setup.sh
```

---

## 🚀 사용 방법

설정 스크립트를 실행합니다:

```bash
./setup.sh
```

일반적인 대화형 흐름:

1. SSID 입력
2. WPA2 비밀번호 입력
3. 비밀번호 확인
4. Raspberry Pi 재부팅

## 📋 사용 안내

1. 저장소를 클론:
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. 디렉터리 이동:
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. 실행 파일 권한 부여:
   `sudo chmod +x setup.sh`
4. 설정 실행:
   `./setup.sh`
5. SSID, 비밀번호, 비밀번호 확인 입력
6. Raspberry Pi 재부팅

## ⚙️ 구성

스크립트는 다음 시스템 파일을 작성하거나 업데이트합니다:

| 파일 | 용도 |
| --- | --- |
| `/etc/dhcpcd.conf` | `wlan0`용 고정 AP 설정 추가 |
| `/etc/sysctl.d/routed-ap.conf` | `net.ipv4.ip_forward=1`로 IPv4 포워딩 활성화 |
| `/etc/dnsmasq.conf` | AP 클라이언트용 DHCP/DNS 설정 (`/etc/dnsmasq.conf.orig`에 기존 백업 이동) |
| `/etc/hostapd/hostapd.conf` | SSID 및 WLAN 무선/보안 설정 저장 |

주요 `hostapd` 기본값:

```ini
country_code=US
interface=wlan0
hw_mode=g
channel=2
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_pairwise=TKIP
wpa_key_mgmt=WPA-PSK
rsn_pairwise=CCMP
```

기본값을 변경하려면 다음 파일을 편집하세요.

- `/etc/hostapd/hostapd.conf`
- `/etc/dnsmasq.conf`(필요한 DNS/DHCP 동작 변경 시)
- `/etc/dhcpcd.conf`(서브넷/주소 변경 시)

## 📝 참고 사항

1. 이 스크립트는 기본적으로 2.4GHz SSID를 채널 `2`에 생성합니다.
2. 국가 코드는 기본값이 `US`입니다.
3. `i18n/`에는 상단 언어 선택기에서 링크되는 번역된 README들이 들어 있습니다.

## 🔍 예시

AP 및 포워딩 설정 점검:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

설정 완료 후 AP 측 예상 정보:

- 액세스 포인트 IP: `10.20.1.1`
- AP 로컬 DNS 이름: `rpi.ap`

## 🛠️ 개발 노트

- 핵심 구현은 `setup.sh`에 집중되어 있습니다.
- 이 저장소에는 테스트 스위트, CI, 패키지 매니페스트가 없습니다.
- 스크립트는 시스템 설정 파일을 직접 수정하므로 모든 파일에 대해 완전한 멱등성을 보장하지 않습니다(예: `/etc/dhcpcd.conf`는 반복 실행 시 중복 라인이 누적될 수 있음).

---

## 🧰 문제 해결

| 증상 | 점검 / 해결 |
| --- | --- |
| `Passwords do not match` | `./setup.sh`를 다시 실행하고 일치하는 값을 다시 입력 |
| AP가 보이지 않음 | 재부팅 후 `systemctl status hostapd` 확인 |
| 클라이언트 장치에서 인터넷이 안 됨 | 이더넷 업링크 확인, `sudo iptables -t nat -S`로 NAT 확인, `/etc/sysctl.d/routed-ap.conf`의 포워딩 설정 확인 |
| 서비스 시작 문제 | `sudo journalctl -u hostapd -b` 및 `sudo journalctl -u dnsmasq -b` 실행 |

## 🧭 로드맵

- 재실행 시 안전성을 높이기 위해 설정 멱등성 확보
- 이전 네트워크 상태를 복원하는 제거/초기화 경로 추가
- 비표준 `wlan0`/`eth0` 시스템에 대한 인터페이스 오버라이드 지원 추가
- 자동화된 스모크 테스트 및 선택적 CI 추가
- `i18n/`에서 번역을 추가/유지

## 🤝 기여

이슈와 풀 리퀘스트를 환영합니다.

권장 기여 점검 항목:

- 문서와 명령이 실제 스크립트 동작과 일치하는지 유지
- Raspberry Pi 실제 하드웨어에서 네트워크 변경 사항 테스트
- 변경된 기본값과 부작용을 문서화

## 📄 라이선스

이 프로젝트는 GNU General Public License v3.0으로 라이선스됩니다.

[LICENSE](LICENSE) 참고.


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
