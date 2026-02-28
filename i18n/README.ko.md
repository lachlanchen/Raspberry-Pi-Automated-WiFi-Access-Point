[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)

Raspberry Pi용 자동화 WiFi 액세스 포인트 설정 스크립트입니다. Pi를 액세스 포인트로 전환하고 이더넷 포트를 통해 트래픽을 전달합니다. 즉, 이 스크립트는 Raspberry Pi를 WiFi 라우터처럼 동작하게 만듭니다.

## 🌐 개요

이 저장소는 Raspberry Pi를 라우팅되는 무선 액세스 포인트로 구성하는 단일 대화형 셸 스크립트 `setup.sh`를 제공합니다.

- WiFi 클라이언트는 `wlan0`에 연결
- Raspberry Pi는 트래픽을 `eth0`로 전달
- `iptables`로 NAT 구성
- `dnsmasq`가 DHCP/DNS 제공
- `hostapd`가 AP 서비스 제공

스크립트가 생성하는 기본 AP 네트워크 값:

| 설정 | 값 |
| --- | --- |
| AP 인터페이스 IP | `10.20.1.1/24` |
| DHCP 범위 | `10.20.1.5` - `10.20.1.100` |
| AP 로컬 이름 | `rpi.ap` |
| WiFi 모드 | 2.4 GHz (`hw_mode=g`) |
| 채널 | `2` |
| 국가 코드 | `US` |

## ✨ 기능

- 대화형 설정(SSID 및 WPA2 비밀번호 입력)
- 패키지 자동 설치:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- `wlan0`에서 AP 네트워크 프로비저닝
- IPv4 포워딩 활성화
- `eth0` 업스트림용 영구 NAT 규칙 구성
- `hostapd` 및 `dnsmasq` 설정 자동 생성
- 부팅 시 `hostapd.service` 활성화

## 🗂️ 프로젝트 구조

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ 사전 요구사항

- Raspberry Pi OS(또는 호환 Debian 기반 이미지)가 설치된 Raspberry Pi
- `wlan0`로 동작하는 WiFi 어댑터
- `eth0`로 사용 가능한 이더넷 업링크
- `sudo` 권한
- `apt-get install`을 위한 인터넷 연결

가정: 스크립트는 전통적인 인터페이스 이름(`wlan0`, `eth0`)을 사용합니다. 시스템에서 다른 이름을 사용하는 경우 스크립트/설정을 그에 맞게 수정하세요.

## 📦 설치

저장소 클론:

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
```

스크립트에 실행 권한 부여:

```bash
sudo chmod +x setup.sh
```

## 🚀 사용법

설정 스크립트 실행:

```bash
./setup.sh
```

그다음:

1. 네트워크 이름(SSID) 입력
2. 비밀번호 입력
3. 비밀번호 확인 입력
4. 재부팅

## 📋 지침

(원본 워크플로우를 정식 기준으로 보존했습니다.)

1. git으로 저장소를 클론합니다  
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. 저장소 디렉터리로 이동합니다  
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. 스크립트에 실행 권한을 부여합니다  
   `sudo chmod +x setup.sh`
4. 스크립트를 실행합니다  
   `./setup.sh`
5. 네트워크 이름, 비밀번호, 비밀번호 확인을 입력합니다
6. 재부팅합니다!

## ⚙️ 구성

스크립트는 다음 파일을 작성/갱신합니다:

| 파일 | 용도 |
| --- | --- |
| `/etc/dhcpcd.conf` | `wlan0`용 정적 AP 구성 추가 |
| `/etc/sysctl.d/routed-ap.conf` | `net.ipv4.ip_forward=1` 설정 |
| `/etc/dnsmasq.conf` | AP DHCP/DNS 구성(원본은 `/etc/dnsmasq.conf.orig`로 이동) |
| `/etc/hostapd/hostapd.conf` | SSID, 보안, 무선 설정 |

### 사용되는 핵심 hostapd 설정

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

설정 후 무선/보안 기본값을 사용자 정의하려면 다음 파일을 편집하세요:

- `/etc/hostapd/hostapd.conf`

## 📝 참고

1. 채널 2의 2.4GHz WiFi 네트워크를 생성합니다
2. 국가 코드는 미국으로 설정됩니다
3. 이 설정을 변경하려면 `/etc/hostapd/hostapd.conf` 파일을 편집하세요
4. 저장소에 `i18n/` 디렉터리가 있으며, 연결된 언어별 README 파일은 시간이 지나며 추가/갱신될 수 있습니다.

## 🔍 예시

구성한 SSID에 기기를 연결한 뒤, Pi에서 라우팅을 확인하세요:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

성공적으로 실행된 후 예상되는 AP 측 접속 정보:

- Raspberry Pi AP IP: `10.20.1.1`
- 로컬 DNS 이름: `rpi.ap`

## 🛠️ 개발 노트

- 주요 로직은 `setup.sh`에 있습니다
- 현재 이 저장소에는 테스트 스위트, CI 파이프라인, 패키지 매니페스트가 없습니다
- 스크립트는 실제 시스템 설정을 직접 작성하는 운영형(명령형) 방식입니다
- 재실행 시 일부 파일(특히 `/etc/dhcpcd.conf`)에 중복 라인이 추가될 수 있습니다

## 🧰 문제 해결

| 문제 | 확인 / 해결 |
| --- | --- |
| `Passwords do not match` | `./setup.sh`를 다시 실행하고 일치하는 값을 다시 입력하세요 |
| 설정 후 AP가 보이지 않음 | Pi를 재부팅한 뒤 `systemctl status hostapd`를 확인하세요 |
| WiFi 클라이언트에서 인터넷 불가 | 이더넷 업링크 인터넷 연결 확인, `sudo iptables -t nat -S`로 NAT 규칙 확인, `cat /etc/sysctl.d/routed-ap.conf`로 포워딩 확인 |
| 서비스 시작 문제 | `sudo journalctl -u hostapd -b` 및 `sudo journalctl -u dnsmasq -b` |

## 🧭 로드맵

- 멱등(idempotent) 설정 로직 추가(안전한 재실행)
- 기존 구성을 복원하는 제거/초기화 스크립트 추가
- 인터페이스 오버라이드 지원 추가(`wlan0`/`eth0`가 아닌 시스템)
- 자동 검증 체크 및 CI 추가
- `i18n/`에 번역 완료본 추가

## 🤝 기여

이슈와 풀 리퀘스트를 환영합니다.

기여 시:

- 명령과 문서가 실제 스크립트 동작과 일치하도록 유지하세요
- 네트워킹 로직 변경 시 실제 Raspberry Pi 하드웨어에서 테스트하세요
- 새로운 기본값(채널, 서브넷, 서비스)은 문서화하세요

## 📄 라이선스

이 프로젝트는 GNU General Public License v3.0을 따릅니다.

자세한 내용은 [LICENSE](LICENSE)를 참조하세요.
