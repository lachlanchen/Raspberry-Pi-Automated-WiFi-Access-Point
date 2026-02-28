[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# Автоматизация точки доступа WiFi на Raspberry Pi

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)
![Config: NAT Routed AP](https://img.shields.io/badge/Config-NAT%20Routed%20AP-2b6cb0)

Автоматизированный скрипт настройки точки доступа WiFi для Raspberry Pi. Он настраивает `wlan0` как точку доступа и маршрутизирует трафик клиентов через `eth0`, используя NAT.

> Превратите Raspberry Pi в надежную маршрутизируемую WiFi-точку доступа с одним настраиваемым скриптом.

##  Snapshot

| Область | Детали |
| --- | --- |
| Область проекта | Автоматизация точки доступа на Raspberry Pi на базе Raspbian/Debian |
| Подсеть AP по умолчанию | `10.20.1.1/24` |
| Диапазон выдачи DHCP | `10.20.1.5`  `10.20.1.100` |
| Параметры WiFi по умолчанию | `hw_mode=g`, канал `2`, страна `US` |
| Обновляемые файлы | `/etc/dhcpcd.conf`, `/etc/hostapd/hostapd.conf`, `/etc/dnsmasq.conf`, `/etc/sysctl.d/routed-ap.conf` |

---

##  Overview

В этом репозитории находится один интерактивный скрипт на shell `setup.sh`, который превращает Raspberry Pi в WiFi-роутер. Он устанавливает и настраивает пакеты и службы, необходимые для маршрутизируемой точки доступа:

- **DHCP/DNS** через `dnsmasq`
- **службу точки доступа** через `hostapd`
- **постоянный NAT и пересылку пакетов** через `iptables`/`netfilter-persistent`

Типовые параметры сети AP, создаваемые скриптом:

| Настройка | Значение |
| --- | --- |
| IP-адрес интерфейса AP | `10.20.1.1/24` |
| DHCP-диапазон | `10.20.1.5`  `10.20.1.100` |
| Локальное имя AP | `rpi.ap` |
| Режим WiFi | 2.4 GHz (`hw_mode=g`) |
| Канал | `2` |
| Код страны | `US` |

## ✨ Features

- Интерактивный ввод SSID и парольной фразы WPA2 (с подтверждением)
- Установка основных зависимостей:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- Настройка статической AP-сети на `wlan0`
- Включение IPv4 forwarding в `/etc/sysctl.d/routed-ap.conf`
- Добавление постоянного правила NAT/маскарадинга для исходящего трафика через `eth0`
- Автоматическая генерация `/etc/dnsmasq.conf` и `/etc/hostapd/hostapd.conf`
- Включение и запуск `hostapd.service` при загрузке

## 🗂 Project Structure

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
 README.md
 setup.sh
 LICENSE
 i18n/
```

## ✅ Prerequisites

- Raspberry Pi с Raspberry Pi OS (или совместимым образом на базе Debian)
- Рабочий WiFi-адаптер, доступный как `wlan0`
- Подключенный Ethernet на `eth0`
- Доступ в Интернет для установки пакетов
- Права `sudo`

Предположение: поведение скрипта рассчитано на классические имена интерфейсов (`wlan0` и `eth0`). Для нестандартных имен потребуется ручная корректировка после настройки.

##  Installation

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
sudo chmod +x setup.sh
```

---

##  Usage

Запустите скрипт настройки:

```bash
./setup.sh
```

Типовой интерактивный процесс:

1. Введите SSID
2. Введите пароль WPA2
3. Подтвердите пароль
4. Перезагрузите Raspberry Pi

##  Instructions

1. Клонируйте репозиторий:
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. Перейдите в каталог:
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. Сделайте скрипт исполняемым:
   `sudo chmod +x setup.sh`
4. Запустите настройку:
   `./setup.sh`
5. Введите SSID, пароль и подтверждение пароля
6. Перезагрузите Raspberry Pi

## ⚙️ Configuration

Скрипт пишет/обновляет следующие системные файлы:

| Файл | Назначение |
| --- | --- |
| `/etc/dhcpcd.conf` | Добавляет статическую конфигурацию AP для `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | Включает пересылку IPv4 через `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | Настраивает DHCP/DNS для клиентов AP (резервная копия перемещается в `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | Хранит SSID и параметры WLAN-радио/безопасности |

Ключевые значения по умолчанию `hostapd`:

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

Для изменения значений по умолчанию отредактируйте:

- `/etc/hostapd/hostapd.conf`
- `/etc/dnsmasq.conf` (при необходимости для поведения DNS/DHCP)
- `/etc/dhcpcd.conf` (для подсети/адресов)

##  Notes

1. Скрипт создаёт SSID на 2.4 GHz на канале `2` по умолчанию.
2. Код страны по умолчанию — `US`.
3. В `i18n/` находятся переведенные варианты README, связанные из верхнего селектора языков.

##  Examples

Проверьте настройки AP и NAT:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Ожидаемые параметры AP после успешной настройки:

- IP-адрес точки доступа: `10.20.1.1`
- Локальное DNS-имя AP: `rpi.ap`

##  Development Notes

- Ядро реализации централизовано в `setup.sh`.
- В репозитории отсутствуют тестовый набор, CI и манифест пакета.
- Скрипт выполняет прямую запись конфигурации системы и не является полностью идемпотентным во всех файлах (например, в `/etc/dhcpcd.conf` при повторных запусках могут накапливаться повторяющиеся строки).

---

##  Troubleshooting

| Проблема | Проверка / Исправление |
| --- | --- |
| `Passwords do not match` | Повторно запустите `./setup.sh` и введите совпадающие значения |
| Точка доступа не видна | Перезагрузите и проверьте `systemctl status hostapd` |
| Нет доступа в Интернет на клиентах | Проверьте uplink Ethernet, проверьте NAT через `sudo iptables -t nat -S`, подтвердите настройки пересылки в `/etc/sysctl.d/routed-ap.conf` |
| Проблемы запуска службы | Выполните `sudo journalctl -u hostapd -b` и `sudo journalctl -u dnsmasq -b` |

## 🧭 Roadmap

- Сделать запуск идемпотентным для более безопасного повторного применения.
- Добавить путь uninstall/reset для восстановления предыдущего состояния сети.
- Добавить поддержку альтернативных интерфейсов для систем без `wlan0`/`eth0`.
- Добавить автоматизированные smoke-тесты и опциональную CI.
- Добавлять и поддерживать переводы в `i18n/`.

## 🤝 Contributing

Issues и pull requests приветствуются.

Рекомендуемые проверки при внесении вклада:

- Поддерживать документацию и команды в соответствии с фактическим поведением скрипта.
- Тестировать изменения в сети на реальном железе Raspberry Pi.
- Документировать изменения значений по умолчанию и побочные эффекты.

## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |

## 📄 License

Этот проект распространяется под лицензией GNU General Public License v3.0.

См. [LICENSE](LICENSE).
