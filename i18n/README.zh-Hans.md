[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)
![Config: NAT Routed AP](https://img.shields.io/badge/Config-NAT%20Routed%20AP-2b6cb0)

这是一个用于 Raspberry Pi 的自动化 WiFi 接入点安装脚本。它将 `wlan0` 配置为 AP，并通过 NAT 将客户端流量经由 `eth0` 转发。

> 使用一个引导式脚本，将 Raspberry Pi 变为稳定的路由型 Wi‑Fi AP。

## 🧩 快照

| 项目 | 说明 |
| --- | --- |
| 项目范围 | 基于 Raspbian/Debian 的 Raspberry Pi 接入点自动化 |
| 默认 AP 子网 | `10.20.1.1/24` |
| DHCP 租约范围 | `10.20.1.5` - `10.20.1.100` |
| Wi‑Fi 射频默认值 | `hw_mode=g`，信道 `2`，国家代码 `US` |
| 脚本化文件 | `/etc/dhcpcd.conf`、`/etc/hostapd/hostapd.conf`、`/etc/dnsmasq.conf`、`/etc/sysctl.d/routed-ap.conf` |

---

## 🌐 概述

本仓库包含一个交互式 shell 脚本 `setup.sh`，可将 Raspberry Pi 配置为 Wi‑Fi 路由器。它会安装并配置路由型接入点所需的软件包和服务：

- 通过 `dnsmasq` 提供 **DHCP/DNS**
- 通过 `hostapd` 提供 **AP 服务**
- 通过 `iptables`/`netfilter-persistent` 提供持久化 NAT 与转发

脚本创建的默认接入点网络参数如下：

| 项目 | 值 |
| --- | --- |
| AP 接口 IP | `10.20.1.1/24` |
| DHCP 范围 | `10.20.1.5` - `10.20.1.100` |
| AP 本地域名 | `rpi.ap` |
| WiFi 模式 | 2.4 GHz（`hw_mode=g`） |
| 信道 | `2` |
| 国家代码 | `US` |

## ✨ 功能特性

- 交互式配置提示：输入 SSID 与 WPA2 密码并确认
- 安装核心依赖：
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- 在 `wlan0` 上配置静态 AP 网络
- 在 `/etc/sysctl.d/routed-ap.conf` 中启用 IPv4 转发
- 为经 `eth0` 出口的流量添加持久化 NAT/MASQUERADE 规则
- 自动生成 `/etc/dnsmasq.conf` 与 `/etc/hostapd/hostapd.conf`
- 开机启动并启用 `hostapd.service`

## 🗂️ 项目结构

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ 先决条件

- 运行 Raspberry Pi OS（或兼容 Debian 衍生镜像）的 Raspberry Pi
- 一块可用的 Wi‑Fi 适配器（接口名为 `wlan0`）
- 一条可用的有线上行链路（接口名为 `eth0`）
- 具备用于安装软件包的互联网访问权限
- `sudo` 权限

前提假设：脚本行为基于经典接口命名（`wlan0` 与 `eth0`）。若接口名非标准，请在安装后手动调整。

## 📦 安装

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
sudo chmod +x setup.sh
```

---

## 🚀 使用方法

运行安装脚本：

```bash
./setup.sh
```

典型交互流程：

1. 输入 SSID
2. 输入 WPA2 密码
3. 确认密码
4. 重启树莓派

## 📋 操作说明

1. 克隆仓库：
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. 切换到仓库目录：
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. 赋予脚本执行权限：
   `sudo chmod +x setup.sh`
4. 运行脚本：
   `./setup.sh`
5. 输入 SSID、密码和密码确认
6. 重启 Raspberry Pi

## ⚙️ 配置

脚本会写入或更新以下系统文件：

| 文件 | 用途 |
| --- | --- |
| `/etc/dhcpcd.conf` | 为 `wlan0` 添加静态 AP 配置 |
| `/etc/sysctl.d/routed-ap.conf` | 通过 `net.ipv4.ip_forward=1` 启用 IPv4 转发 |
| `/etc/dnsmasq.conf` | 为 AP 客户端配置 DHCP/DNS（原文件会移到 `/etc/dnsmasq.conf.orig`） |
| `/etc/hostapd/hostapd.conf` | 保存 SSID 与 WLAN 射频/安全配置 |

关键 `hostapd` 默认值：

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

如需自定义默认值，请编辑：

- `/etc/hostapd/hostapd.conf`
- `/etc/dnsmasq.conf`（如需调整 DNS/DHCP 行为）
- `/etc/dhcpcd.conf`（用于调整子网和地址）

## 📝 说明

1. 本脚本默认在信道 `2` 创建 2.4GHz SSID。
2. 国家代码默认值为 `US`。
3. `i18n/` 包含顶部语言切换所链接的 README 翻译。

## 🔍 示例

检查 AP 与转发设置：

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

成功配置后的 AP 端预期结果：

- 接入点 IP：`10.20.1.1`
- AP 本地 DNS 名称：`rpi.ap`

## 🛠️ 开发说明

- 核心实现集中在 `setup.sh` 中。
- 仓库中未包含测试套件、CI 或打包清单。
- 脚本直接写入系统配置，且在所有文件中不完全幂等（例如，`/etc/dhcpcd.conf` 在多次执行后可能会累积重复行）。

---

## 🧰 故障排查

| 问题 | 检查 / 处理 |
| --- | --- |
| `Passwords do not match` | 重新运行 `./setup.sh` 并再次输入匹配的值 |
| AP 无法被发现 | 重启后检查 `systemctl status hostapd` |
| 客户端无网络访问 | 检查以太网上行链路，使用 `sudo iptables -t nat -S` 检查 NAT，确认 `/etc/sysctl.d/routed-ap.conf` 中的转发设置 |
| 服务启动异常 | 运行 `sudo journalctl -u hostapd -b` 与 `sudo journalctl -u dnsmasq -b` |

## 🧭 路线图

- 增加幂等性以便更安全地重复运行。
- 添加卸载/重置路径以恢复先前的网络状态。
- 为非 `wlan0`/`eth0` 系统添加接口覆盖支持。
- 增加自动化冒烟检查并增加可选 CI。
- 在 `i18n/` 下补充和维护翻译。

## 🤝 贡献

欢迎提交 issue 与 pull request。

推荐的提交检查项：

- 使文档与命令与脚本行为保持一致。
- 在真实的 Raspberry Pi 硬件上验证网络变更。
- 记录任何默认值与副作用的变更。

## 📄 许可证

本项目基于 GNU General Public License v3.0 发布。

查看 [LICENSE](LICENSE)。


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
