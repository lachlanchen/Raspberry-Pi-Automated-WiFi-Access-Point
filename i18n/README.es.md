[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)
![Config: NAT Routed AP](https://img.shields.io/badge/Config-NAT%20Routed%20AP-2b6cb0)

Un script automatizado de configuración de punto de acceso WiFi para Raspberry Pi. Configura `wlan0` como AP y enruta el tráfico de clientes a través de `eth0` usando NAT.

> Convierte una Raspberry Pi en un AP Wi‑Fi enrutado fiable con un único script guiado.

## 🧩 Snapshot

| Área | Detalles |
| --- | --- |
| Alcance del proyecto | Automatización de punto de acceso Raspberry Pi basado en Raspbian/Debian |
| Subred AP predeterminada | `10.20.1.1/24` |
| Rango de concesión DHCP | `10.20.1.5` - `10.20.1.100` |
| Valores predeterminados de Wi‑Fi | `hw_mode=g`, canal `2`, país `US` |
| Archivos configurados | `/etc/dhcpcd.conf`, `/etc/hostapd/hostapd.conf`, `/etc/dnsmasq.conf`, `/etc/sysctl.d/routed-ap.conf` |

---

## 🌐 Visión general

Este repositorio contiene un único script interactivo de shell, `setup.sh`, que convierte una Raspberry Pi en un router Wi‑Fi. Instala y configura los paquetes y servicios necesarios para un punto de acceso enrutado:

- **DHCP/DNS** mediante `dnsmasq`
- **daemon de AP** mediante `hostapd`
- **NAT persistente y reenvío de paquetes** mediante `iptables`/`netfilter-persistent`

Valores de red de punto de acceso predeterminados creados por el script:

| Ajuste | Valor |
| --- | --- |
| IP de interfaz AP | `10.20.1.1/24` |
| Rango DHCP | `10.20.1.5` - `10.20.1.100` |
| Nombre local del AP | `rpi.ap` |
| Modo WiFi | 2.4 GHz (`hw_mode=g`) |
| Canal | `2` |
| Código de país | `US` |

## ✨ Características

- Configuración interactiva con solicitudes de SSID y contraseña WPA2 (con confirmación)
- Instala dependencias básicas:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- Configura red AP estática en `wlan0`
- Habilita reenvío IPv4 en `/etc/sysctl.d/routed-ap.conf`
- Añade regla NAT/masquerade persistente para el tráfico saliente por `eth0`
- Genera automáticamente `/etc/dnsmasq.conf` y `/etc/hostapd/hostapd.conf`
- Habilita e inicia `hostapd.service` al arranque

## 🗂️ Estructura del proyecto

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ Requisitos previos

- Raspberry Pi con Raspberry Pi OS (o imagen compatible basada en Debian)
- Un adaptador Wi‑Fi funcional expuesto como `wlan0`
- Una conexión ascendente por Ethernet en `eth0`
- Acceso a Internet para la instalación de paquetes
- Privilegios `sudo`

Supuesto: el comportamiento del script asume nombres de interfaz clásicos (`wlan0` y `eth0`). Los nombres no estándar requieren ajuste manual tras la configuración.

## 📦 Instalación

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
sudo chmod +x setup.sh
```

---

## 🚀 Uso

Ejecuta el script de configuración:

```bash
./setup.sh
```

Flujo interactivo típico:

1. Introduce el SSID
2. Introduce la contraseña WPA2
3. Confirma la contraseña
4. Reinicia la Pi

## 📋 Instrucciones

1. Clona este repositorio:
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. Cambia de directorio:
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. Haz el script ejecutable:
   `sudo chmod +x setup.sh`
4. Ejecuta la configuración:
   `./setup.sh`
5. Introduce SSID, contraseña y confirmación de contraseña
6. Reinicia la Raspberry Pi

## ⚙️ Configuración

El script escribe/actualiza estos archivos del sistema:

| Archivo | Propósito |
| --- | --- |
| `/etc/dhcpcd.conf` | Añade configuración AP estática para `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | Habilita reenvío IPv4 mediante `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | Configura DHCP/DNS para clientes AP (la copia anterior se mueve a `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | Guarda SSID y ajustes de radio/seguridad WLAN |

Ajustes clave predeterminados de `hostapd`:

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

Para personalizar valores predeterminados, edita:

- `/etc/hostapd/hostapd.conf`
- `/etc/dnsmasq.conf` (si necesitas cambiar el comportamiento DNS/DHCP)
- `/etc/dhcpcd.conf` (para subredes/direcciones)

## 📝 Notas

1. Este script crea por defecto un SSID de 2.4GHz en el canal `2`.
2. El código de país por defecto es `US`.
3. `i18n/` contiene variantes traducidas del README enlazadas desde el selector de idioma superior.

## 🔍 Ejemplos

Inspecciona la configuración de AP y reenvío:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Detalles esperados del lado AP tras un setup exitoso:

- IP del punto de acceso: `10.20.1.1`
- Nombre DNS local del AP: `rpi.ap`

## 🛠️ Notas de desarrollo

- La implementación principal está centralizada en `setup.sh`.
- Este repositorio no incluye suite de pruebas, CI ni manifiesto de paquetes.
- El script escribe directamente archivos de configuración del sistema y no es completamente idempotente en todos los archivos (por ejemplo, `/etc/dhcpcd.conf` puede acumular líneas repetidas en ejecuciones repetidas).

---

## 🧰 Solución de problemas

| Incidencia | Verificar / Arreglar |
| --- | --- |
| `Passwords do not match` | Vuelve a ejecutar `./setup.sh` y vuelve a introducir valores coincidentes |
| El AP no es visible | Reinicia y revisa `systemctl status hostapd` |
| Los clientes no tienen acceso a internet | Verifica el enlace Ethernet, inspecciona NAT con `sudo iptables -t nat -S`, confirma ajustes de reenvío en `/etc/sysctl.d/routed-ap.conf` |
| Problemas al iniciar servicios | Ejecuta `sudo journalctl -u hostapd -b` y `sudo journalctl -u dnsmasq -b` |

## 🧭 Hoja de ruta

- Hacer la configuración idempotente para reruns más seguros.
- Añadir ruta de desinstalación/restablecimiento para recuperar el estado de red previo.
- Añadir soporte de sobreescritura de interfaz para sistemas no `wlan0`/`eth0`.
- Añadir comprobaciones automáticas y CI opcional.
- Mantener traducciones bajo `i18n/`.

## 🤝 Contribuir

Se aceptan incidencias y pull requests.

Revisiones recomendadas de aportes:

- Mantén documentación y comandos alineados con el comportamiento real del script.
- Prueba cambios de red en hardware Raspberry Pi real.
- Documenta cualquier cambio de valores por defecto y efectos secundarios.

## 📄 Licencia

Este proyecto está licenciado bajo la GNU General Public License v3.0.

Consulta [LICENSE](../LICENSE).


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
