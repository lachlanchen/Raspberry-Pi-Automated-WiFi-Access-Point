[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)
![Config: NAT Routed AP](https://img.shields.io/badge/Config-NAT%20Routed%20AP-2b6cb0)

這是一個用於 Raspberry Pi 的自動化 Wi‑Fi 存取點設定腳本。它會將 `wlan0` 設定為 AP，並透過 NAT 將用戶端流量經由 `eth0` 轉送。

> 透過一個引導式腳本，將 Raspberry Pi 變成穩定的路由式 Wi‑Fi AP。

## 🧩 快照

| 項目 | 說明 |
| --- | --- |
| 專案範圍 | 基於 Raspbian/Debian 的 Raspberry Pi 存取點自動化 |
| 預設 AP 子網 | `10.20.1.1/24` |
| DHCP 租約範圍 | `10.20.1.5` - `10.20.1.100` |
| Wi‑Fi 無線電預設值 | `hw_mode=g`、通道 `2`、國家代碼 `US` |
| 腳本化檔案 | `/etc/dhcpcd.conf`、`/etc/hostapd/hostapd.conf`、`/etc/dnsmasq.conf`、`/etc/sysctl.d/routed-ap.conf` |

---

## 🌐 概覽

本儲存庫包含一個互動式 shell 腳本 `setup.sh`，可讓 Raspberry Pi 變成 Wi‑Fi 路由器。它會安裝並設定路由型接入點所需的套件與服務：

- 透過 `dnsmasq` 提供 **DHCP/DNS**
- 透過 `hostapd` 提供 **AP 服務**
- 透過 `iptables`/`netfilter-persistent` 提供持續的 NAT 與封包轉送

腳本建立的預設接入點網路參數如下：

| 項目 | 值 |
| --- | --- |
| AP 介面 IP | `10.20.1.1/24` |
| DHCP 範圍 | `10.20.1.5` - `10.20.1.100` |
| AP 本地域名 | `rpi.ap` |
| Wi-Fi 模式 | 2.4 GHz（`hw_mode=g`） |
| 通道 | `2` |
| 國家代碼 | `US` |

## ✨ 功能特性

- 互動式設定流程：輸入 SSID 與 WPA2 密碼（含再次確認）
- 安裝核心相依套件：
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- 在 `wlan0` 上設定靜態 AP 網路
- 在 `/etc/sysctl.d/routed-ap.conf` 啟用 IPv4 轉送
- 新增持續生效的 NAT/MASQUERADE 規則，將外送流量導向 `eth0`
- 自動產生 `/etc/dnsmasq.conf` 與 `/etc/hostapd/hostapd.conf`
- 開機時啟用並啟動 `hostapd.service`

## 🗂️ 專案架構

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ 前置需求

- 執行 Raspberry Pi OS（或相容 Debian 衍生映像）的 Raspberry Pi
- 可用的 Wi‑Fi 配接器，顯示為 `wlan0`
- `eth0` 的乙太網路上行連線
- 有可用的網際網路連線以便安裝套件
- `sudo` 權限

預設情境下，腳本行為假設介面名稱為傳統格式（`wlan0` 與 `eth0`）。若使用其他名稱，需在完成安裝後手動調整。

## 📦 安裝

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
sudo chmod +x setup.sh
```

---

## 🚀 使用方式

執行安裝腳本：

```bash
./setup.sh
```

典型的互動流程：

1. 輸入 SSID
2. 輸入 WPA2 密碼
3. 確認密碼
4. 重新開機 Raspberry Pi

## 📋 操作說明

1. 複製此儲存庫：
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. 切換目錄：
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. 讓腳本可執行：
   `sudo chmod +x setup.sh`
4. 執行安裝：
   `./setup.sh`
5. 輸入 SSID、密碼與密碼確認
6. 重新開機 Raspberry Pi

## ⚙️ 設定

腳本會寫入或更新以下系統檔案：

| 檔案 | 用途 |
| --- | --- |
| `/etc/dhcpcd.conf` | 為 `wlan0` 新增 AP 靜態設定 |
| `/etc/sysctl.d/routed-ap.conf` | 透過 `net.ipv4.ip_forward=1` 啟用 IPv4 轉送 |
| `/etc/dnsmasq.conf` | 為 AP 用戶端設定 DHCP/DNS（原檔將移至 `/etc/dnsmasq.conf.orig`） |
| `/etc/hostapd/hostapd.conf` | 儲存 SSID 與 WLAN 無線電/安全性設定 |

主要 `hostapd` 預設值：

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

如需自訂預設值，請編輯：

- `/etc/hostapd/hostapd.conf`
- `/etc/dnsmasq.conf`（若需調整 DNS/DHCP 行為）
- `/etc/dhcpcd.conf`（調整子網與位址）

## 📝 注意事項

1. 此腳本預設會在通道 `2` 建立 2.4GHz SSID。
2. 國家代碼預設為 `US`。
3. `i18n/` 包含從頂部語系切換器連到的 README 翻譯版本。

## 🔍 範例

檢查 AP 與轉送設定：

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

成功設定後 AP 端預期結果：

- 接入點 IP：`10.20.1.1`
- AP 本地 DNS 名稱：`rpi.ap`

## 🛠️ 開發筆記

- 核心實作集中在 `setup.sh`。
- 本儲存庫未提供測試套件、CI，亦未提供套件清單。
- 腳本會直接寫入系統設定檔，且在全部檔案中不完全具備冪等性（例如 `/etc/dhcpcd.conf` 在重複執行時可能會累積重複行）。

---

## 🧰 故障排除

| 問題 | 檢查 / 修正 |
| --- | --- |
| `Passwords do not match` | 重新執行 `./setup.sh` 並重新輸入一致的值 |
| 無法看到 AP | 重新開機後檢查 `systemctl status hostapd` |
| 用戶端無法連網 | 檢查乙太網路上行鏈路，使用 `sudo iptables -t nat -S` 檢查 NAT，確認 `/etc/sysctl.d/routed-ap.conf` 中的轉送設定 |
| 服務啟動異常 | 執行 `sudo journalctl -u hostapd -b` 與 `sudo journalctl -u dnsmasq -b` |

## 🧭 發展藍圖

- 讓設定流程更具冪等性，以便安全地重複執行。
- 新增移除/重置流程，以還原先前的網路狀態。
- 新增非 `wlan0`/`eth0` 系統的介面覆蓋支援。
- 新增自動化冒煙測試並提供可選 CI。
- 在 `i18n/` 下新增並維護更多語系翻譯。

## 🤝 貢獻

歡迎回報 issue 與提出 pull request。

建議的提交流程檢查：

- 讓文件與指令與實際腳本行為一致。
- 在實體 Raspberry Pi 硬體上驗證網路變更。
- 記錄任何預設值與副作用的變更。

## 📄 授權

本專案以 GNU 通用公共授權條款 v3.0 發布。

請見 [LICENSE](LICENSE)。


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
