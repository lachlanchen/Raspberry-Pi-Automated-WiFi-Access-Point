[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)

Ein automatisiertes Setup-Skript für einen WiFi-Access-Point auf Raspberry Pis. Es verwandelt deinen Pi in einen Access Point, der Datenverkehr über den Ethernet-Port weiterleitet. Kurz gesagt: Dieses Skript macht aus deinem Raspberry Pi einen WiFi-Router.

## 🌐 Überblick

Dieses Repository enthält ein einzelnes interaktives Shell-Skript, `setup.sh`, das einen Raspberry Pi als gerouteten WLAN-Access-Point konfiguriert:

- WiFi-Clients verbinden sich mit `wlan0`
- Der Raspberry Pi leitet den Datenverkehr an `eth0` weiter
- NAT wird mit `iptables` konfiguriert
- DHCP/DNS werden durch `dnsmasq` bereitgestellt
- Der AP-Dienst wird durch `hostapd` bereitgestellt

Standardwerte für das AP-Netzwerk, die vom Skript erstellt werden:

| Einstellung | Wert |
| --- | --- |
| AP-Interface-IP | `10.20.1.1/24` |
| DHCP-Bereich | `10.20.1.5` - `10.20.1.100` |
| Lokaler AP-Name | `rpi.ap` |
| WiFi-Modus | 2,4 GHz (`hw_mode=g`) |
| Kanal | `2` |
| Ländercode | `US` |

## ✨ Funktionen

- Interaktives Setup (fragt SSID und WPA2-Passphrase ab)
- Automatische Paketinstallation:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- AP-Netzwerkbereitstellung auf `wlan0`
- Aktivierung von IPv4-Forwarding
- Persistente NAT-Regel für Upstream über `eth0`
- Automatisch generierte Konfiguration für `hostapd` und `dnsmasq`
- Aktiviert `hostapd.service` für den Systemstart

## 🗂️ Projektstruktur

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ Voraussetzungen

- Raspberry Pi mit Raspberry Pi OS (oder kompatibles Debian-basiertes Image)
- Funktionsfähiger WLAN-Adapter als `wlan0`
- Ethernet-Uplink als `eth0` verfügbar
- `sudo`-Berechtigungen
- Internetzugang für `apt-get install`

Annahme: Das Skript verwendet klassische Interface-Namen (`wlan0`, `eth0`). Falls dein System andere Namen verwendet, passe Skript/Konfiguration entsprechend an.

## 📦 Installation

Repository klonen:

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
```

Skript ausführbar machen:

```bash
sudo chmod +x setup.sh
```

## 🚀 Verwendung

Setup-Skript ausführen:

```bash
./setup.sh
```

Dann:

1. Netzwerknamen (SSID) eingeben
2. Passwort eingeben
3. Passwort bestätigen
4. Neustarten

## 📋 Anleitung

(Originalablauf als kanonische Basis beibehalten.)

1. Repository mit git klonen  
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. In das Repository-Verzeichnis wechseln  
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. Skript ausführbar machen  
   `sudo chmod +x setup.sh`
4. Skript ausführen  
   `./setup.sh`
5. Netzwerkname, Passwort und Passwortbestätigung eingeben
6. Neustarten!

## ⚙️ Konfiguration

Das Skript schreibt/aktualisiert diese Dateien:

| Datei | Zweck |
| --- | --- |
| `/etc/dhcpcd.conf` | Fügt statische AP-Konfiguration für `wlan0` hinzu |
| `/etc/sysctl.d/routed-ap.conf` | Setzt `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | AP-DHCP/DNS-Konfiguration (Original wird nach `/etc/dnsmasq.conf.orig` verschoben) |
| `/etc/hostapd/hostapd.conf` | SSID-, Sicherheits- und Funk-Einstellungen |

### Verwendete zentrale hostapd-Einstellungen

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

Um Funk-/Sicherheits-Standardwerte nach dem Setup anzupassen, bearbeite:

- `/etc/hostapd/hostapd.conf`

## 📝 Hinweise

1. Dadurch wird ein 2,4-GHz-WiFi-Netz auf Kanal 2 erstellt
2. Der Ländercode ist auf Vereinigte Staaten gesetzt
3. Um diese Einstellungen zu ändern, bearbeite die Datei `/etc/hostapd/hostapd.conf`
4. `i18n/` existiert im Repository; verlinkte Sprach-README-Dateien können dort im Laufe der Zeit hinzugefügt/aktualisiert werden.

## 🔍 Beispiele

Verbinde ein Gerät mit deiner konfigurierten SSID und prüfe dann das Routing vom Pi aus:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Erwartete AP-seitige Zugriffsdaten nach erfolgreichem Durchlauf:

- Raspberry Pi AP-IP: `10.20.1.1`
- Lokaler DNS-Name: `rpi.ap`

## 🛠️ Entwicklungshinweise

- Die Hauptlogik liegt in `setup.sh`
- Dieses Repository hat derzeit keine Test-Suite, keine CI-Pipeline und kein Paketmanifest
- Das Skript ist operativ und imperativ (schreibt direkt in die Systemkonfiguration)
- Erneutes Ausführen kann in einigen Dateien doppelte Zeilen anhängen (insbesondere in `/etc/dhcpcd.conf`)

## 🧰 Fehlerbehebung

| Problem | Prüfung / Behebung |
| --- | --- |
| `Passwords do not match` | `./setup.sh` erneut ausführen und passende Werte erneut eingeben |
| AP nach dem Setup nicht sichtbar | Pi neu starten, dann `systemctl status hostapd` prüfen |
| Kein Internet auf WiFi-Clients | Internet am Ethernet-Uplink bestätigen, NAT-Regel mit `sudo iptables -t nat -S` prüfen, Forwarding mit `cat /etc/sysctl.d/routed-ap.conf` prüfen |
| Probleme beim Dienststart | `sudo journalctl -u hostapd -b` und `sudo journalctl -u dnsmasq -b` |

## 🧭 Roadmap

- Idempotente Setup-Logik hinzufügen (sichere Wiederholungsdurchläufe)
- Uninstall-/Reset-Skript hinzufügen, um frühere Konfiguration wiederherzustellen
- Unterstützung für Interface-Overrides hinzufügen (Systeme ohne `wlan0`/`eth0`)
- Automatisierte Validierungsprüfungen und CI hinzufügen
- Vollständige Übersetzungen unter `i18n/` ergänzen

## 🤝 Mitwirken

Issues und Pull Requests sind willkommen.

Beim Mitwirken:

- Befehle und Dokumentation mit dem tatsächlichen Skriptverhalten synchron halten
- Änderungen an Netzwerkkonfiguration möglichst auf echter Raspberry-Pi-Hardware testen
- Neue Standardwerte (Kanäle, Subnetze, Dienste) dokumentieren

## 📄 Lizenz

Dieses Projekt ist unter der GNU General Public License v3.0 lizenziert.

Siehe [LICENSE](LICENSE).
