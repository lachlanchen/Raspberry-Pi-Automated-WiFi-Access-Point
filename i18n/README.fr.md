[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)
![Config: NAT Routed AP](https://img.shields.io/badge/Config-NAT%20Routed%20AP-2b6cb0)

Un script d’installation automatisé pour créer un point d’accès Wi‑Fi sur Raspberry Pi. Il configure `wlan0` comme AP et fait transiter le trafic des clients via `eth0` avec le NAT.

> Transformez un Raspberry Pi en AP Wi‑Fi routé fiable en une seule exécution guidée.

## 🧩 Snapshot

| Domaine | Détails |
| --- | --- |
| Portée du projet | Automatisation d’un point d’accès Raspberry Pi basé sur Raspbian/Debian |
| Sous-réseau AP par défaut | `10.20.1.1/24` |
| Plage DHCP | `10.20.1.5` - `10.20.1.100` |
| Paramètres Wi‑Fi par défaut | `hw_mode=g`, canal `2`, pays `US` |
| Fichiers configurés par le script | `/etc/dhcpcd.conf`, `/etc/hostapd/hostapd.conf`, `/etc/dnsmasq.conf`, `/etc/sysctl.d/routed-ap.conf` |

---

## 🌐 Vue d’ensemble

Ce dépôt contient un unique script shell interactif, `setup.sh`, qui transforme un Raspberry Pi en routeur Wi‑Fi. Il installe et configure les paquets et services nécessaires à un point d’accès routé :

- **DHCP/DNS** via `dnsmasq`
- **Démon AP** via `hostapd`
- **NAT persistant et relais de paquets** via `iptables`/`netfilter-persistent`

Valeurs réseau de point d’accès créées par défaut par le script :

| Paramètre | Valeur |
| --- | --- |
| Adresse IP de l’interface AP | `10.20.1.1/24` |
| Plage DHCP | `10.20.1.5` - `10.20.1.100` |
| Nom local AP | `rpi.ap` |
| Mode WiFi | 2.4 GHz (`hw_mode=g`) |
| Canal | `2` |
| Code pays | `US` |

## ✨ Fonctionnalités

- Invite de configuration interactive pour le SSID et la phrase de passe WPA2 (avec confirmation)
- Installe les dépendances principales :
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- Configure un réseau AP statique sur `wlan0`
- Active le relais IPv4 dans `/etc/sysctl.d/routed-ap.conf`
- Ajoute une règle NAT/Masquerade persistante pour le trafic sortant via `eth0`
- Génère automatiquement `/etc/dnsmasq.conf` et `/etc/hostapd/hostapd.conf`
- Active et démarre `hostapd.service` au démarrage

## 🗂️ Structure du projet

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ Pré-requis

- Raspberry Pi exécutant Raspberry Pi OS (ou une image Debian compatible)
- Un adaptateur Wi‑Fi fonctionnel exposé en `wlan0`
- Une liaison ethernet sur `eth0`
- Un accès Internet pour l’installation des paquets
- Des privilèges `sudo`

Hypothèse : le comportement du script suppose les noms d’interface classiques (`wlan0` et `eth0`). Les noms non standards nécessitent un ajustement manuel après l’installation.

## 📦 Installation

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
sudo chmod +x setup.sh
```

---

## 🚀 Utilisation

Lancez le script d’installation :

```bash
./setup.sh
```

Flux interactif typique :

1. Saisir le SSID
2. Saisir la passphrase WPA2
3. Confirmer la passphrase
4. Redémarrer le Pi

## 📋 Instructions

1. Cloner ce dépôt :
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. Entrer dans le dossier :
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. Rendre le script exécutable :
   `sudo chmod +x setup.sh`
4. Lancer l’installation :
   `./setup.sh`
5. Saisir le SSID, le mot de passe et confirmer le mot de passe
6. Redémarrer le Raspberry Pi

## ⚙️ Configuration

Le script écrit/actualise ces fichiers système :

| Fichier | Objectif |
| --- | --- |
| `/etc/dhcpcd.conf` | Ajoute la configuration AP statique pour `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | Active le relais IPv4 via `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | Configure DHCP/DNS pour les clients AP (la sauvegarde est déplacée vers `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | Stocke le SSID et les paramètres radio/sécurité WLAN |

Paramètres `hostapd` principaux :

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

Pour personnaliser les valeurs par défaut, modifiez :

- `/etc/hostapd/hostapd.conf`
- `/etc/dnsmasq.conf` (si besoin pour le comportement DNS/DHCP)
- `/etc/dhcpcd.conf` (pour le sous-réseau et les adresses)

## 📝 Remarques

1. Ce script crée par défaut un SSID 2.4 GHz sur le canal `2`.
2. Le code pays est `US` par défaut.
3. Le dossier `i18n/` contient les versions traduites du README, liées depuis le sélecteur de langues en haut.

## 🔍 Exemples

Vérifier la configuration AP et le relais :

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Détails attendus côté AP après une installation réussie :

- Adresse IP du point d’accès : `10.20.1.1`
- Nom DNS local de l’AP : `rpi.ap`

## 🛠️ Notes de développement

- L’implémentation principale est centralisée dans `setup.sh`.
- Le dépôt ne contient ni suite de tests, ni CI, ni manifeste de paquets.
- Le script écrit directement la configuration système et n’est pas entièrement idempotent pour tous les fichiers (par exemple, `/etc/dhcpcd.conf` peut accumuler des lignes en doublon lors de relances successives).

---

## 🧰 Dépannage

| Problème | Vérification / correction |
| --- | --- |
| `Passwords do not match` | Relancez `./setup.sh` et entrez de nouveau des valeurs identiques |
| AP non visible | Redémarrez et vérifiez `systemctl status hostapd` |
| Aucun accès Internet sur les appareils clients | Vérifiez la liaison Ethernet, inspectez le NAT avec `sudo iptables -t nat -S`, confirmez les réglages de relais dans `/etc/sysctl.d/routed-ap.conf` |
| Problèmes de démarrage des services | Exécutez `sudo journalctl -u hostapd -b` et `sudo journalctl -u dnsmasq -b` |

## 🧭 Feuille de route

- Rendre l’installation idempotente pour des relances plus sûres.
- Ajouter un chemin de désinstallation/réinitialisation pour restaurer l’état réseau précédent.
- Ajouter la prise en charge de l’override d’interface pour les systèmes non `wlan0`/`eth0`.
- Ajouter des vérifications automatiques et un CI optionnel.
- Ajouter et maintenir les traductions sous `i18n/`.

## 🤝 Contribution

Les issues et pull requests sont les bienvenues.

Vérifications recommandées pour les contributions :

- Gardez la documentation et les commandes alignées avec le comportement réel du script.
- Testez les changements réseau sur du matériel Raspberry Pi réel.
- Documentez toute modification des valeurs par défaut et des effets secondaires.

## 📄 Licence

Ce projet est distribué sous licence GNU General Public License v3.0.

Voir [LICENSE](../LICENSE).


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
