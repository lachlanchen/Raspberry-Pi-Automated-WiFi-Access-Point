[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)

这是一个适用于 Raspberry Pi 的自动化 WiFi 接入点配置脚本。它可将你的 Pi 变为一个通过以太网口转发流量的接入点。本质上，该脚本会把 Raspberry Pi 转换成 WiFi 路由器。

## 🌐 概览

本仓库提供一个交互式 shell 脚本 `setup.sh`，用于将 Raspberry Pi 配置为路由型无线接入点：

- WiFi 客户端连接到 `wlan0`
- Raspberry Pi 将流量转发到 `eth0`
- 使用 `iptables` 配置 NAT
- 由 `dnsmasq` 提供 DHCP/DNS
- 由 `hostapd` 提供 AP 服务

脚本创建的默认 AP 网络参数：

| 设置 | 值 |
| --- | --- |
| AP 接口 IP | `10.20.1.1/24` |
| DHCP 范围 | `10.20.1.5` - `10.20.1.100` |
| AP 本地名称 | `rpi.ap` |
| WiFi 模式 | 2.4 GHz (`hw_mode=g`) |
| 信道 | `2` |
| 国家代码 | `US` |

## ✨ 功能特性

- 交互式配置（会提示输入 SSID 和 WPA2 密码）
- 自动安装软件包：
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- 在 `wlan0` 上完成 AP 网络配置
- 启用 IPv4 转发
- 为 `eth0` 上游配置持久化 NAT 规则
- 自动生成 `hostapd` 和 `dnsmasq` 配置
- 启用 `hostapd.service` 开机启动

## 🗂️ 项目结构

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ 前置条件

- 搭载 Raspberry Pi OS（或兼容 Debian 的镜像）的 Raspberry Pi
- 可正常工作的 WiFi 适配器（接口名为 `wlan0`）
- 可用的以太网上行链路（接口名为 `eth0`）
- `sudo` 权限
- 用于 `apt-get install` 的互联网连接

假设：脚本使用经典接口命名（`wlan0`、`eth0`）。如果你的系统使用不同接口名，请相应更新脚本/配置。

## 📦 安装

克隆仓库：

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
```

为脚本添加可执行权限：

```bash
sudo chmod +x setup.sh
```

## 🚀 使用方法

运行配置脚本：

```bash
./setup.sh
```

然后：

1. 输入网络名称（SSID）
2. 输入密码
3. 确认密码
4. 重启

## 📋 操作说明

（保留原始流程作为规范基础。）

1. 使用 git 克隆仓库  
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. 进入仓库目录  
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. 为脚本添加可执行权限  
   `sudo chmod +x setup.sh`
4. 运行脚本  
   `./setup.sh`
5. 输入网络名称、密码及密码确认
6. 重启！

## ⚙️ 配置

脚本会写入/更新以下文件：

| 文件 | 用途 |
| --- | --- |
| `/etc/dhcpcd.conf` | 为 `wlan0` 添加静态 AP 配置 |
| `/etc/sysctl.d/routed-ap.conf` | 设置 `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | AP 的 DHCP/DNS 配置（原文件移动到 `/etc/dnsmasq.conf.orig`） |
| `/etc/hostapd/hostapd.conf` | SSID、安全和无线电参数 |

### 使用的关键 hostapd 设置

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

如需在配置完成后自定义无线电/安全默认值，请编辑：

- `/etc/hostapd/hostapd.conf`

## 📝 说明

1. 这会创建一个运行在信道 2 的 2.4GHz WiFi 网络
2. 国家代码设置为美国
3. 如需修改这些设置，请编辑 `/etc/hostapd/hostapd.conf` 文件
4. 仓库中存在 `i18n/`；其中链接的多语言 README 文件会随时间增加/更新。

## 🔍 示例

将设备连接到你配置的 SSID，然后在 Pi 上验证路由：

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

成功运行后，AP 侧的预期访问信息：

- Raspberry Pi AP IP：`10.20.1.1`
- 本地 DNS 名称：`rpi.ap`

## 🛠️ 开发说明

- 主要逻辑位于 `setup.sh`
- 本仓库目前没有测试套件、CI 流水线或包清单
- 脚本以操作式方式运行（直接写入系统配置）
- 重复运行可能会在某些文件中追加重复行（尤其是 `/etc/dhcpcd.conf`）

## 🧰 故障排查

| 问题 | 检查 / 修复 |
| --- | --- |
| `Passwords do not match` | 重新运行 `./setup.sh` 并输入一致的值 |
| 配置后看不到 AP | 重启 Pi，然后检查 `systemctl status hostapd` |
| WiFi 客户端无法上网 | 确认以太网上行有互联网，使用 `sudo iptables -t nat -S` 验证 NAT 规则，使用 `cat /etc/sysctl.d/routed-ap.conf` 验证转发配置 |
| 服务启动问题 | `sudo journalctl -u hostapd -b` 和 `sudo journalctl -u dnsmasq -b` |

## 🧭 路线图

- 添加幂等配置逻辑（可安全重复运行）
- 添加卸载/重置脚本以恢复先前配置
- 添加接口覆盖支持（适配非 `wlan0`/`eth0` 系统）
- 添加自动化校验与 CI
- 在 `i18n/` 下补充完整翻译

## 🤝 贡献

欢迎提交 Issue 和 Pull Request。

贡献时请注意：

- 保持命令与文档和脚本实际行为一致
- 修改网络逻辑时请在真实 Raspberry Pi 硬件上测试
- 记录任何新增默认值（信道、子网、服务）

## 📄 许可证

本项目采用 GNU General Public License v3.0 许可证。

参见 [LICENSE](LICENSE)。
