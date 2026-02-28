[English](README.md) · [العربية](i18n/README.ar.md) · [Español](i18n/README.es.md) · [Français](i18n/README.fr.md) · [日本語](i18n/README.ja.md) · [한국어](i18n/README.ko.md) · [Tiếng Việt](i18n/README.vi.md) · [中文 (简体)](i18n/README.zh-Hans.md) · [中文（繁體）](i18n/README.zh-Hant.md) · [Deutsch](i18n/README.de.md) · [Русский](i18n/README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)
![Config: NAT Routed AP](https://img.shields.io/badge/Config-NAT%20Routed%20AP-2b6cb0)

An automated WiFi Access Point setup script for Raspberry Pi. It configures `wlan0` as an AP and routes client traffic through `eth0` using NAT.

> Turn a Raspberry Pi into a reliable routed Wi‑Fi AP with one guided script.

## 🧩 Snapshot

| Area | Details |
| --- | --- |
| Project scope | Raspbian/Debian-based Raspberry Pi access point automation |
| Default AP subnet | `10.20.1.1/24` |
| DHCP lease range | `10.20.1.5` - `10.20.1.100` |
| Wi‑Fi radio defaults | `hw_mode=g`, channel `2`, country `US` |
| Scripted files | `/etc/dhcpcd.conf`, `/etc/hostapd/hostapd.conf`, `/etc/dnsmasq.conf`, `/etc/sysctl.d/routed-ap.conf` |

---

## 🌐 Overview

This repository contains a single interactive shell script, `setup.sh`, that turns a Raspberry Pi into a Wi‑Fi router. It installs and configures the packages and services needed for a routed access point:

- **DHCP/DNS** via `dnsmasq`
- **AP daemon** via `hostapd`
- **Persistent NAT and packet forwarding** via `iptables`/`netfilter-persistent`

Default access point network values created by the script:

| Setting | Value |
| --- | --- |
| AP interface IP | `10.20.1.1/24` |
| DHCP range | `10.20.1.5` - `10.20.1.100` |
| AP local name | `rpi.ap` |
| WiFi mode | 2.4 GHz (`hw_mode=g`) |
| Channel | `2` |
| Country code | `US` |

## ✨ Features

- Interactive setup prompts for SSID and WPA2 passphrase (with confirmation)
- Installs core dependencies:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- Configures static AP network on `wlan0`
- Enables IPv4 forwarding in `/etc/sysctl.d/routed-ap.conf`
- Adds persistent NAT/masquerade rule for outbound traffic through `eth0`
- Auto-generates `/etc/dnsmasq.conf` and `/etc/hostapd/hostapd.conf`
- Enables and starts `hostapd.service` at boot

## 🗂️ Project Structure

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ Prerequisites

- Raspberry Pi running Raspberry Pi OS (or compatible Debian-based image)
- A working Wi‑Fi adapter exposed as `wlan0`
- An ethernet uplink on `eth0`
- Internet access for package installation
- `sudo` privileges

Assumption: script behavior assumes classic interface names (`wlan0` and `eth0`). Non-standard names require manual adjustment after setup.

## 📦 Installation

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
sudo chmod +x setup.sh
```

---

## 🚀 Usage

Run the setup script:

```bash
./setup.sh
```

Typical interactive flow:

1. Enter SSID
2. Enter WPA2 passphrase
3. Confirm passphrase
4. Reboot the Pi

## 📋 Instructions

1. Clone this repository:
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. Change directory:
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. Make the script executable:
   `sudo chmod +x setup.sh`
4. Run setup:
   `./setup.sh`
5. Enter SSID, password, and password confirmation
6. Reboot the Raspberry Pi

## ⚙️ Configuration

The script writes/updates these system files:

| File | Purpose |
| --- | --- |
| `/etc/dhcpcd.conf` | Adds static AP config for `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | Enables IPv4 forwarding via `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | Configures DHCP/DNS for AP clients (backup moved to `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | Stores SSID and WLAN radio/security settings |

Key `hostapd` defaults:

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

To customize defaults, edit:

- `/etc/hostapd/hostapd.conf`
- `/etc/dnsmasq.conf` (if needed for DNS/DHCP behavior)
- `/etc/dhcpcd.conf` (for subnet/addresses)

## 📝 Notes

1. This script creates a 2.4GHz SSID on channel `2` by default.
2. Country code defaults to `US`.
3. `i18n/` contains translated README variants linked from the top language selector.

## 🔍 Examples

Inspect AP and forwarding setup:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Expected AP side details after successful setup:

- Access point IP: `10.20.1.1`
- AP local DNS name: `rpi.ap`

## 🛠️ Development Notes

- Core implementation is centralized in `setup.sh`.
- No test suite, CI, or package manifest is present in this repository.
- Script performs direct system configuration writes and is not fully idempotent in all files (for example `/etc/dhcpcd.conf` may accumulate repeated lines on repeated runs).

---

## 🧰 Troubleshooting

| Issue | Check / Fix |
| --- | --- |
| `Passwords do not match` | Rerun `./setup.sh` and re-enter matching values |
| AP not visible | Reboot and check `systemctl status hostapd` |
| No internet access on client devices | Verify Ethernet uplink, inspect NAT with `sudo iptables -t nat -S`, confirm forwarding settings in `/etc/sysctl.d/routed-ap.conf` |
| Service startup problems | Run `sudo journalctl -u hostapd -b` and `sudo journalctl -u dnsmasq -b` |

## 🧭 Roadmap

- Make setup idempotent for safer re-runs.
- Add uninstall/reset path to restore previous networking state.
- Add interface override support for non-`wlan0`/`eth0` systems.
- Add automated smoke checks and optional CI.
- Add and maintain translations under `i18n/`.

## 🤝 Contributing

Issues and pull requests are welcome.

Recommended contribution checks:

- Keep documentation and commands aligned with actual script behavior.
- Test networking changes on real Raspberry Pi hardware.
- Document any changed default values and side effects.

## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |

## 📄 License

This project is licensed under the GNU General Public License v3.0.

See [LICENSE](LICENSE).
