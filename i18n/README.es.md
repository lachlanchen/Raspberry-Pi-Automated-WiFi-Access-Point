[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)

Un script automatizado para configurar un punto de acceso WiFi en Raspberry Pi. Convierte tu Pi en un punto de acceso que reenvía el tráfico a través del puerto Ethernet. En esencia, este script transforma tu Raspberry Pi en un router WiFi.

## 🌐 Resumen

Este repositorio proporciona un único script interactivo de shell, `setup.sh`, que configura una Raspberry Pi como punto de acceso inalámbrico enrutado:

- Los clientes WiFi se conectan a `wlan0`
- La Raspberry Pi reenvía el tráfico a `eth0`
- Se configura NAT con `iptables`
- `dnsmasq` proporciona DHCP/DNS
- `hostapd` proporciona el servicio de AP

Valores de red AP predeterminados que crea el script:

| Ajuste | Valor |
| --- | --- |
| IP de la interfaz AP | `10.20.1.1/24` |
| Rango DHCP | `10.20.1.5` - `10.20.1.100` |
| Nombre local del AP | `rpi.ap` |
| Modo WiFi | 2.4 GHz (`hw_mode=g`) |
| Canal | `2` |
| Código de país | `US` |

## ✨ Características

- Configuración interactiva (solicita SSID y contraseña WPA2)
- Instalación automática de paquetes:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- Aprovisionamiento de red AP en `wlan0`
- Habilitación del reenvío IPv4
- Regla NAT persistente para el enlace ascendente en `eth0`
- Configuración de `hostapd` y `dnsmasq` generada automáticamente
- Habilita `hostapd.service` para el arranque

## 🗂️ Estructura del Proyecto

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ Requisitos Previos

- Raspberry Pi con Raspberry Pi OS (o una imagen compatible basada en Debian)
- Adaptador WiFi funcional como `wlan0`
- Enlace ascendente por Ethernet disponible como `eth0`
- Privilegios de `sudo`
- Acceso a Internet para `apt-get install`

Suposición: el script usa nombres de interfaz clásicos (`wlan0`, `eth0`). Si tu sistema usa nombres diferentes, actualiza el script/la configuración en consecuencia.

## 📦 Instalación

Clona el repositorio:

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
```

Haz que el script sea ejecutable:

```bash
sudo chmod +x setup.sh
```

## 🚀 Uso

Ejecuta el script de configuración:

```bash
./setup.sh
```

Luego:

1. Introduce el nombre de red (SSID)
2. Introduce la contraseña
3. Confirma la contraseña
4. Reinicia

## 📋 Instrucciones

(Flujo de trabajo original preservado como base canónica.)

1. Clona el repositorio usando git  
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. Cambia al directorio del repositorio  
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. Haz que el script sea ejecutable  
   `sudo chmod +x setup.sh`
4. Ejecuta el script  
   `./setup.sh`
5. Introduce nombre de red, contraseña y confirmación de contraseña
6. Reinicia

## ⚙️ Configuración

El script escribe/actualiza estos archivos:

| Archivo | Propósito |
| --- | --- |
| `/etc/dhcpcd.conf` | Añade configuración AP estática para `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | Establece `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | Configuración DHCP/DNS del AP (el original se mueve a `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | SSID, seguridad y ajustes de radio |

### Ajustes clave de hostapd usados

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

Para personalizar los valores predeterminados de radio/seguridad tras la configuración, edita:

- `/etc/hostapd/hostapd.conf`

## 📝 Notas

1. Esto creará una red WiFi de 2.4GHz en el canal 2
2. El código de país está configurado como Estados Unidos
3. Para cambiar estos ajustes, edita el archivo `/etc/hostapd/hostapd.conf`
4. `i18n/` existe en el repositorio; los archivos README enlazados por idioma pueden añadirse/actualizarse allí con el tiempo.

## 🔍 Ejemplos

Conecta un dispositivo a tu SSID configurado y luego verifica el enrutamiento desde la Pi:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Detalles de acceso esperados del lado AP tras una ejecución correcta:

- IP AP de la Raspberry Pi: `10.20.1.1`
- Nombre DNS local: `rpi.ap`

## 🛠️ Notas de Desarrollo

- La lógica principal está en `setup.sh`
- Este repositorio actualmente no tiene suite de pruebas, pipeline de CI ni manifiesto de paquetes
- El script es operativo e imperativo (escribe directamente en la configuración del sistema)
- Volver a ejecutarlo puede añadir líneas duplicadas en algunos archivos (en particular `/etc/dhcpcd.conf`)

## 🧰 Solución de Problemas

| Problema | Verificar / Solución |
| --- | --- |
| `Passwords do not match` | Vuelve a ejecutar `./setup.sh` e introduce valores coincidentes |
| AP no visible después de la configuración | Reinicia la Pi y luego verifica `systemctl status hostapd` |
| Sin Internet en clientes WiFi | Confirma que el enlace Ethernet tiene Internet, verifica la regla NAT con `sudo iptables -t nat -S`, verifica el reenvío con `cat /etc/sysctl.d/routed-ap.conf` |
| Problemas de inicio de servicios | `sudo journalctl -u hostapd -b` y `sudo journalctl -u dnsmasq -b` |

## 🧭 Hoja de Ruta

- Añadir lógica de configuración idempotente (re-ejecuciones seguras)
- Añadir script de desinstalación/restablecimiento para restaurar la configuración previa
- Añadir soporte para sobrescribir interfaces (sistemas no `wlan0`/`eth0`)
- Añadir validaciones automáticas y CI
- Añadir traducciones completadas en `i18n/`

## 🤝 Contribuir

Se aceptan issues y pull requests.

Al contribuir:

- Mantén comandos y documentación alineados con el comportamiento real del script
- Prueba en hardware Raspberry Pi real cuando cambies lógica de red
- Documenta cualquier nuevo valor predeterminado (canales, subredes, servicios)

## 📄 Licencia

Este proyecto está licenciado bajo GNU General Public License v3.0.

Consulta [LICENSE](../LICENSE).
