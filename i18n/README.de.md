[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)
![Config: NAT Routed AP](https://img.shields.io/badge/Config-NAT%20Routed%20AP-2b6cb0)

Ein automatisiertes Setup-Skript für einen WiFi-Access-Point auf dem Raspberry Pi. Es richtet `wlan0` als AP ein und leitet den Client-Verkehr über NAT durch `eth0`.

> Wandle einen Raspberry Pi mit einem geführten Skript in einen zuverlässigen gerouteten Wi‑Fi-AP um.

## 🧩 Snapshot

| Bereich | Details |
| --- | --- |
| Projektumfang | Raspbian/Debian-basierte Raspberry-Pi-Access-Point-Automatisierung |
| Standard-AP-Subnetz | `10.20.1.1/24` |
| DHCP-Mietzeitbereich | `10.20.1.5` - `10.20.1.100` |
| WLAN-Radio-Standards | `hw_mode=g`, Kanal `2`, Land `US` |
| Konfigurierte Dateien | `/etc/dhcpcd.conf`, `/etc/hostapd/hostapd.conf`, `/etc/dnsmasq.conf`, `/etc/sysctl.d/routed-ap.conf` |

---

## 🌐 Übersicht

Dieses Repository enthält ein einzelnes interaktives Shell-Skript, `setup.sh`, das einen Raspberry Pi in einen WLAN-Router verwandelt. Es installiert und konfiguriert die Pakete und Dienste, die für einen gerouteten Access Point erforderlich sind:

- **DHCP/DNS** über `dnsmasq`
- **AP-Dienst** über `hostapd`
- **Persistente NAT-Weiterleitung und Paketweiterleitung** über `iptables`/`netfilter-persistent`

Standardwerte für das Access-Point-Netzwerk, die vom Skript gesetzt werden:

| Einstellung | Wert |
| --- | --- |
| IP der AP-Schnittstelle | `10.20.1.1/24` |
| DHCP-Bereich | `10.20.1.5` - `10.20.1.100` |
| AP-Hostname | `rpi.ap` |
| WiFi-Modus | 2,4 GHz (`hw_mode=g`) |
| Kanal | `2` |
| Ländercode | `US` |

## ✨ Funktionen

- Interaktive Setup-Eingaben für SSID und WPA2-Passphrase (mit Bestätigung)
- Installiert Kernabhängigkeiten:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- Konfiguriert ein statisches AP-Netzwerk auf `wlan0`
- Aktiviert IPv4-Forwarding in `/etc/sysctl.d/routed-ap.conf`
- Fügt eine persistente NAT/Masquerade-Regel für ausgehenden Verkehr über `eth0` hinzu
- Generiert automatisch `/etc/dnsmasq.conf` und `/etc/hostapd/hostapd.conf`
- Aktiviert und startet `hostapd.service` beim Booten

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
- Ein funktionierender WLAN-Adapter, der als `wlan0` erkannt wird
- Ein Ethernet-Uplink auf `eth0`
- Internetzugang für die Paketinstallation
- `sudo`-Berechtigungen

Annahme: Das Skriptverhalten basiert auf klassischen Schnittstellennamen (`wlan0` und `eth0`). Nicht standardisierte Namen erfordern eine manuelle Anpassung nach der Einrichtung.

## 📦 Installation

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
sudo chmod +x setup.sh
```

---

## 🚀 Verwendung

Führe das Setup-Skript aus:

```bash
./setup.sh
```

Typischer interaktiver Ablauf:

1. SSID eingeben
2. WPA2-Passphrase eingeben
3. Passphrase bestätigen
4. Raspberry Pi neu starten

## 📋 Anleitung

1. Repository klonen:
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. Verzeichnis wechseln:
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. Skript ausführbar machen:
   `sudo chmod +x setup.sh`
4. Einrichtung starten:
   `./setup.sh`
5. SSID, Passwort und Passwortbestätigung eingeben
6. Raspberry Pi neu starten

## ⚙️ Konfiguration

Das Skript schreibt/aktualisiert diese Systemdateien:

| Datei | Zweck |
| --- | --- |
| `/etc/dhcpcd.conf` | Fügt statische AP-Konfiguration für `wlan0` hinzu |
| `/etc/sysctl.d/routed-ap.conf` | Aktiviert IPv4-Forwarding über `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | Konfiguriert DHCP/DNS für AP-Clients (Sicherungsdatei nach `/etc/dnsmasq.conf.orig` verschoben) |
| `/etc/hostapd/hostapd.conf` | Speichert SSID- und WLAN-Radio-/Sicherheitskonfiguration |

Wichtige `hostapd`-Standardwerte:

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

Zum Anpassen der Standardwerte bearbeiten Sie:

- `/etc/hostapd/hostapd.conf`
- `/etc/dnsmasq.conf` (falls nötig für DNS-/DHCP-Verhalten)
- `/etc/dhcpcd.conf` (für Subnetz/Adressen)

## 📝 Hinweise

1. Dieses Skript erstellt standardmäßig eine 2,4GHz-SSID auf Kanal `2`.
2. Der Ländercode ist standardmäßig `US`.
3. Im Ordner `i18n/` befinden sich Übersetzungsvarianten der README, die über die Sprachauswahl oben verlinkt sind.

## 🔍 Beispiele

AP- und Weiterleitungskonfiguration prüfen:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Erwartete AP-spezifische Details nach erfolgreicher Einrichtung:

- Access-Point-IP: `10.20.1.1`
- Lokaler AP-DNS-Name: `rpi.ap`

## 🛠️ Entwicklungshinweise

- Die Kernimplementierung ist zentral in `setup.sh` enthalten.
- Im Repository gibt es keine Test-Suite, CI oder Paketdefinition.
- Das Skript schreibt Systemkonfiguration direkt und ist nicht in allen Dateien vollständig idempotent (zum Beispiel kann `/etc/dhcpcd.conf` bei wiederholten Läufen wiederholte Zeilen enthalten).

---

## 🧰 Fehlerbehebung

| Problem | Prüfen / Beheben |
| --- | --- |
| `Passwords do not match` | `./setup.sh` erneut ausführen und passende Werte erneut eingeben |
| AP nicht sichtbar | Neu starten und `systemctl status hostapd` prüfen |
| Kein Internetzugang bei Clients | Ethernet-Uplink prüfen, NAT mit `sudo iptables -t nat -S` inspizieren, Forwarding-Einstellungen in `/etc/sysctl.d/routed-ap.conf` bestätigen |
| Probleme beim Dienststart | `sudo journalctl -u hostapd -b` und `sudo journalctl -u dnsmasq -b` ausführen |

## 🧭 Roadmap

- Einrichtung idempotenter machen, damit Wiederholungen sicherer sind.
- Pfad für Deinstallation/Zurücksetzen hinzufügen, um den vorherigen Netzwerkzustand wiederherzustellen.
- Unterstützung für Interface-Override bei nicht-`wlan0`/`eth0`-Systemen hinzufügen.
- Automatisierte Smoke-Checks und optionale CI hinzufügen.
- Übersetzungen unter `i18n/` hinzufügen und pflegen.

## 🤝 Mitwirken

Fehler und Pull Requests sind willkommen.

Empfohlene Prüfungsschritte für Beiträge:

- Dokumentation und Befehle an das tatsächliche Skriptverhalten anpassen.
- Netzwerkänderungen auf echter Raspberry-Pi-Hardware testen.
- Geänderte Standardwerte und Nebenwirkungen dokumentieren.

## 📄 Lizenz

Dieses Projekt ist unter der GNU General Public License v3.0 lizenziert.

Siehe [LICENSE](LICENSE).


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
