[English](README.md) · [العربية](i18n/README.ar.md) · [Español](i18n/README.es.md) · [Français](i18n/README.fr.md) · [日本語](i18n/README.ja.md) · [한국어](i18n/README.ko.md) · [Tiếng Việt](i18n/README.vi.md) · [中文 (简体)](i18n/README.zh-Hans.md) · [中文（繁體）](i18n/README.zh-Hant.md) · [Deutsch](i18n/README.de.md) · [Русский](i18n/README.ru.md)


# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)

An automated WiFi Access Point setup script for Raspberry Pis. Turns your Pi into an access point that forwards traffic through the ethernet port. In essence, this script converts your Raspberry Pi into a WiFi router.

## 🌐 Overview

This repository provides a single interactive shell script, `setup.sh`, that configures a Raspberry Pi as a routed wireless access point:

- WiFi clients connect to `wlan0`
- Raspberry Pi forwards traffic to `eth0`
- NAT is configured with `iptables`
- DHCP/DNS are provided by `dnsmasq`
- AP service is provided by `hostapd`

Default AP network values created by the script:

| Setting | Value |
| --- | --- |
| AP interface IP | `10.20.1.1/24` |
| DHCP range | `10.20.1.5` - `10.20.1.100` |
| AP local name | `rpi.ap` |
| WiFi mode | 2.4 GHz (`hw_mode=g`) |
| Channel | `2` |
| Country code | `US` |

## ✨ Features

- Interactive setup (asks for SSID and WPA2 passphrase)
- Automatic package installation:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- AP network provisioning on `wlan0`
- IPv4 forwarding enablement
- Persistent NAT rule for upstream on `eth0`
- Auto-generated `hostapd` and `dnsmasq` config
- Enables `hostapd.service` for boot

## 🗂️ Project Structure

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ Prerequisites

- Raspberry Pi with Raspberry Pi OS (or compatible Debian-based image)
- Functional WiFi adapter as `wlan0`
- Ethernet uplink available as `eth0`
- `sudo` privileges
- Internet access for `apt-get install`

Assumption: The script uses classic interface names (`wlan0`, `eth0`). If your system uses different names, update the script/config accordingly.

## 📦 Installation

Clone the repository:

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
```

Make the script executable:

```bash
sudo chmod +x setup.sh
```

## 🚀 Usage

Run the setup script:

```bash
./setup.sh
```

Then:

1. Enter network name (SSID)
2. Enter password
3. Confirm password
4. Reboot

## 📋 Instructions

(Original workflow preserved as canonical base.)

1. Clone the repository using git  
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. Change directory into the repository  
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. Make the script executable  
   `sudo chmod +x setup.sh`
4. Run the script  
   `./setup.sh`
5. Enter network name, password, and password confirmation
6. Reboot!

## ⚙️ Configuration

The script writes/updates these files:

| File | Purpose |
| --- | --- |
| `/etc/dhcpcd.conf` | Adds static AP config for `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | Sets `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | AP DHCP/DNS config (original moved to `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | SSID, security, and radio settings |

### Key hostapd settings used

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

To customize radio/security defaults after setup, edit:

- `/etc/hostapd/hostapd.conf`

## 📝 Notes

1. This will create a 2.4GHz WiFi network on channel 2
2. Country code is set to United States
3. To change these settings, edit the `/etc/hostapd/hostapd.conf` file
4. `i18n/` exists in the repository; linked language README files may be added/updated there over time.

## 🔍 Examples

Connect a device to your configured SSID, then verify routing from the Pi:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Expected AP-side access details after a successful run:

- Raspberry Pi AP IP: `10.20.1.1`
- Local DNS name: `rpi.ap`

## 🛠️ Development Notes

- Main logic lives in `setup.sh`
- This repository currently has no test suite, CI pipeline, or package manifest
- The script is operational and imperative (writes directly to system config)
- Re-running may append duplicate lines in some files (`/etc/dhcpcd.conf` in particular)

## 🧰 Troubleshooting

| Issue | Check / Fix |
| --- | --- |
| `Passwords do not match` | Rerun `./setup.sh` and re-enter matching values |
| AP not visible after setup | Reboot the Pi, then check `systemctl status hostapd` |
| No internet on WiFi clients | Confirm Ethernet uplink internet, verify NAT rule with `sudo iptables -t nat -S`, verify forwarding with `cat /etc/sysctl.d/routed-ap.conf` |
| Service startup issues | `sudo journalctl -u hostapd -b` and `sudo journalctl -u dnsmasq -b` |

## 🧭 Roadmap

- Add idempotent setup logic (safe re-runs)
- Add uninstall/reset script to restore prior config
- Add interface override support (non-`wlan0`/`eth0` systems)
- Add automated validation checks and CI
- Add completed translations under `i18n/`

## 🤝 Contributing

Issues and pull requests are welcome.

When contributing:

- Keep commands and docs aligned with real script behavior
- Test on actual Raspberry Pi hardware when changing networking logic
- Document any new defaults (channels, subnets, services)

## 📄 License

This project is licensed under the GNU General Public License v3.0.

See [LICENSE](LICENSE).
