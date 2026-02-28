[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)

Автоматизированный скрипт настройки WiFi Access Point для Raspberry Pi. Он превращает ваш Pi в точку доступа, которая перенаправляет трафик через Ethernet-порт. По сути, этот скрипт делает из Raspberry Pi WiFi-роутер.

## 🌐 Обзор

Этот репозиторий содержит один интерактивный shell-скрипт, `setup.sh`, который настраивает Raspberry Pi как маршрутизируемую беспроводную точку доступа:

- WiFi-клиенты подключаются к `wlan0`
- Raspberry Pi перенаправляет трафик на `eth0`
- NAT настраивается через `iptables`
- DHCP/DNS предоставляются через `dnsmasq`
- Сервис точки доступа обеспечивается `hostapd`

Значения сети AP по умолчанию, которые создаёт скрипт:

| Параметр | Значение |
| --- | --- |
| IP интерфейса AP | `10.20.1.1/24` |
| Диапазон DHCP | `10.20.1.5` - `10.20.1.100` |
| Локальное имя AP | `rpi.ap` |
| Режим WiFi | 2.4 ГГц (`hw_mode=g`) |
| Канал | `2` |
| Код страны | `US` |

## ✨ Возможности

- Интерактивная настройка (запрашивает SSID и WPA2-пароль)
- Автоматическая установка пакетов:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- Развёртывание сети AP на `wlan0`
- Включение IPv4 forwarding
- Постоянное правило NAT для восходящего канала через `eth0`
- Автоматическая генерация конфигураций `hostapd` и `dnsmasq`
- Включение `hostapd.service` для запуска при загрузке

## 🗂️ Структура проекта

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ Требования

- Raspberry Pi с Raspberry Pi OS (или совместимым Debian-based образом)
- Рабочий WiFi-адаптер как `wlan0`
- Доступный Ethernet uplink как `eth0`
- Права `sudo`
- Доступ в интернет для `apt-get install`

Предположение: скрипт использует классические имена интерфейсов (`wlan0`, `eth0`). Если в вашей системе используются другие имена, соответствующим образом обновите скрипт/конфигурацию.

## 📦 Установка

Клонируйте репозиторий:

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
```

Сделайте скрипт исполняемым:

```bash
sudo chmod +x setup.sh
```

## 🚀 Использование

Запустите скрипт настройки:

```bash
./setup.sh
```

Затем:

1. Введите имя сети (SSID)
2. Введите пароль
3. Подтвердите пароль
4. Перезагрузитесь

## 📋 Инструкции

(Оригинальный рабочий процесс сохранён как каноническая база.)

1. Клонируйте репозиторий с помощью git  
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. Перейдите в каталог репозитория  
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. Сделайте скрипт исполняемым  
   `sudo chmod +x setup.sh`
4. Запустите скрипт  
   `./setup.sh`
5. Введите имя сети, пароль и подтверждение пароля
6. Перезагрузитесь!

## ⚙️ Конфигурация

Скрипт записывает/обновляет следующие файлы:

| Файл | Назначение |
| --- | --- |
| `/etc/dhcpcd.conf` | Добавляет статическую конфигурацию AP для `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | Устанавливает `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | Конфигурация AP DHCP/DNS (оригинал переносится в `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | Настройки SSID, безопасности и радио |

### Ключевые используемые настройки hostapd

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

Чтобы изменить стандартные радиопараметры/параметры безопасности после настройки, отредактируйте:

- `/etc/hostapd/hostapd.conf`

## 📝 Примечания

1. Будет создана WiFi-сеть 2.4 ГГц на канале 2
2. Код страны установлен на Соединённые Штаты
3. Чтобы изменить эти параметры, отредактируйте файл `/etc/hostapd/hostapd.conf`
4. В репозитории есть `i18n/`; связанные языковые README могут со временем добавляться/обновляться там.

## 🔍 Примеры

Подключите устройство к настроенному SSID, затем проверьте маршрутизацию на Pi:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Ожидаемые данные доступа со стороны AP после успешного запуска:

- IP AP Raspberry Pi: `10.20.1.1`
- Локальное DNS-имя: `rpi.ap`

## 🛠️ Заметки по разработке

- Основная логика находится в `setup.sh`
- В этом репозитории пока нет набора тестов, CI-пайплайна или package manifest
- Скрипт операционный и императивный (напрямую записывает системную конфигурацию)
- Повторный запуск может добавлять дублирующиеся строки в некоторые файлы (в частности, в `/etc/dhcpcd.conf`)

## 🧰 Устранение неполадок

| Проблема | Проверка / Исправление |
| --- | --- |
| `Passwords do not match` | Повторно запустите `./setup.sh` и снова введите совпадающие значения |
| AP не виден после настройки | Перезагрузите Pi, затем проверьте `systemctl status hostapd` |
| Нет интернета у WiFi-клиентов | Убедитесь, что интернет доступен через Ethernet uplink, проверьте правило NAT через `sudo iptables -t nat -S`, проверьте forwarding через `cat /etc/sysctl.d/routed-ap.conf` |
| Проблемы при запуске сервисов | `sudo journalctl -u hostapd -b` и `sudo journalctl -u dnsmasq -b` |

## 🧭 Дорожная карта

- Добавить идемпотентную логику настройки (безопасные повторные запуски)
- Добавить скрипт удаления/сброса для восстановления предыдущей конфигурации
- Добавить поддержку переопределения интерфейсов (системы без `wlan0`/`eth0`)
- Добавить автоматические проверки валидации и CI
- Добавить завершённые переводы в `i18n/`

## 🤝 Участие в проекте

Issues и pull requests приветствуются.

При внесении изменений:

- Поддерживайте соответствие команд и документации реальному поведению скрипта
- При изменении сетевой логики тестируйте на реальном оборудовании Raspberry Pi
- Документируйте любые новые значения по умолчанию (каналы, подсети, сервисы)

## 📄 Лицензия

Этот проект распространяется по лицензии GNU General Public License v3.0.

См. [LICENSE](LICENSE).
