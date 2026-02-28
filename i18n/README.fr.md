[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)

Un script automatisé de configuration de point d'accès WiFi pour Raspberry Pi. Il transforme votre Pi en point d'accès qui relaie le trafic via le port Ethernet. En pratique, ce script convertit votre Raspberry Pi en routeur WiFi.

## 🌐 Vue d'ensemble

Ce dépôt fournit un unique script shell interactif, `setup.sh`, qui configure un Raspberry Pi comme point d'accès sans fil routé :

- Les clients WiFi se connectent à `wlan0`
- Le Raspberry Pi redirige le trafic vers `eth0`
- Le NAT est configuré avec `iptables`
- Le DHCP/DNS est fourni par `dnsmasq`
- Le service de point d'accès est assuré par `hostapd`

Valeurs réseau AP par défaut créées par le script :

| Paramètre | Valeur |
| --- | --- |
| IP de l'interface AP | `10.20.1.1/24` |
| Plage DHCP | `10.20.1.5` - `10.20.1.100` |
| Nom local de l'AP | `rpi.ap` |
| Mode WiFi | 2.4 GHz (`hw_mode=g`) |
| Canal | `2` |
| Code pays | `US` |

## ✨ Fonctionnalités

- Configuration interactive (demande le SSID et la phrase secrète WPA2)
- Installation automatique des paquets :
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- Provisionnement du réseau AP sur `wlan0`
- Activation du transfert IPv4
- Règle NAT persistante pour la liaison montante sur `eth0`
- Génération automatique de la configuration `hostapd` et `dnsmasq`
- Active `hostapd.service` au démarrage

## 🗂️ Structure du projet

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ Prérequis

- Raspberry Pi avec Raspberry Pi OS (ou image Debian compatible)
- Adaptateur WiFi fonctionnel en tant que `wlan0`
- Liaison montante Ethernet disponible en tant que `eth0`
- Privilèges `sudo`
- Accès Internet pour `apt-get install`

Hypothèse : le script utilise les noms d'interface classiques (`wlan0`, `eth0`). Si votre système utilise des noms différents, mettez le script/la configuration à jour en conséquence.

## 📦 Installation

Cloner le dépôt :

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
```

Rendre le script exécutable :

```bash
sudo chmod +x setup.sh
```

## 🚀 Utilisation

Exécuter le script d'installation :

```bash
./setup.sh
```

Puis :

1. Saisir le nom du réseau (SSID)
2. Saisir le mot de passe
3. Confirmer le mot de passe
4. Redémarrer

## 📋 Instructions

(Flux de travail d'origine conservé comme base canonique.)

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

Le script écrit/met à jour ces fichiers :

| Fichier | Rôle |
| --- | --- |
| `/etc/dhcpcd.conf` | Ajoute une configuration AP statique pour `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | Définit `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | Configuration DHCP/DNS AP (l'original est déplacé vers `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | Paramètres SSID, sécurité et radio |

### Principaux paramètres hostapd utilisés

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

Pour personnaliser les paramètres radio/sécurité par défaut après l'installation, modifiez :

- `/etc/hostapd/hostapd.conf`

## 📝 Notes

1. Cela crée un réseau WiFi 2.4GHz sur le canal 2
2. Le code pays est défini sur États-Unis
3. Pour modifier ces paramètres, éditez le fichier `/etc/hostapd/hostapd.conf`
4. `i18n/` existe dans le dépôt ; les fichiers README de langue liés peuvent y être ajoutés/mis à jour au fil du temps.

## 🔍 Exemples

Connectez un appareil à votre SSID configuré, puis vérifiez le routage depuis le Pi :

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Détails d'accès côté AP attendus après une exécution réussie :

- IP AP du Raspberry Pi : `10.20.1.1`
- Nom DNS local : `rpi.ap`

## 🛠️ Notes de développement

- La logique principale se trouve dans `setup.sh`
- Ce dépôt ne dispose actuellement ni d'une suite de tests, ni de pipeline CI, ni de manifeste de paquets
- Le script est opérationnel et impératif (il écrit directement dans la configuration système)
- Une réexécution peut ajouter des lignes en double dans certains fichiers (`/etc/dhcpcd.conf` en particulier)

## 🧰 Dépannage

| Problème | Vérification / Correction |
| --- | --- |
| `Passwords do not match` | Relancez `./setup.sh` et ressaisissez des valeurs identiques |
| AP non visible après configuration | Redémarrez le Pi, puis vérifiez `systemctl status hostapd` |
| Pas d'Internet sur les clients WiFi | Confirmez l'accès Internet de la liaison montante Ethernet, vérifiez la règle NAT avec `sudo iptables -t nat -S`, vérifiez le forwarding avec `cat /etc/sysctl.d/routed-ap.conf` |
| Problèmes de démarrage de service | `sudo journalctl -u hostapd -b` et `sudo journalctl -u dnsmasq -b` |

## 🧭 Feuille de route

- Ajouter une logique de configuration idempotente (réexécutions sûres)
- Ajouter un script de désinstallation/réinitialisation pour restaurer la configuration précédente
- Ajouter la prise en charge de la surcharge d'interfaces (systèmes non-`wlan0`/`eth0`)
- Ajouter des vérifications de validation automatisées et une CI
- Ajouter les traductions finalisées dans `i18n/`

## 🤝 Contribution

Les issues et pull requests sont bienvenues.

Lors d'une contribution :

- Maintenir la cohérence entre les commandes/la documentation et le comportement réel du script
- Tester sur du matériel Raspberry Pi réel lors de modifications de la logique réseau
- Documenter toute nouvelle valeur par défaut (canaux, sous-réseaux, services)

## 📄 Licence

Ce projet est sous licence GNU General Public License v3.0.

Voir [LICENSE](LICENSE).
