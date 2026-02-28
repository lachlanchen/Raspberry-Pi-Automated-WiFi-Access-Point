[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)

這是一個用於 Raspberry Pi 的自動化 WiFi 存取點（Access Point）設定腳本。它會將你的 Pi 設定為可透過乙太網路埠轉送流量的 AP。簡單來說，這個腳本會把你的 Raspberry Pi 變成一台 WiFi 路由器。

## 🌐 概覽

此儲存庫提供一個互動式 shell 腳本 `setup.sh`，可將 Raspberry Pi 設定為具路由功能的無線存取點：

- WiFi 用戶端連線到 `wlan0`
- Raspberry Pi 將流量轉送到 `eth0`
- 使用 `iptables` 設定 NAT
- 由 `dnsmasq` 提供 DHCP/DNS
- 由 `hostapd` 提供 AP 服務

腳本建立的預設 AP 網路值：

| 設定 | 值 |
| --- | --- |
| AP 介面 IP | `10.20.1.1/24` |
| DHCP 範圍 | `10.20.1.5` - `10.20.1.100` |
| AP 本機名稱 | `rpi.ap` |
| WiFi 模式 | 2.4 GHz (`hw_mode=g`) |
| 頻道 | `2` |
| 國家代碼 | `US` |

## ✨ 功能

- 互動式設定（會詢問 SSID 與 WPA2 密碼）
- 自動安裝套件：
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- 在 `wlan0` 進行 AP 網路佈建
- 啟用 IPv4 轉送
- 針對 `eth0` 上游建立可持久化的 NAT 規則
- 自動產生 `hostapd` 與 `dnsmasq` 設定
- 啟用開機啟動的 `hostapd.service`

## 🗂️ 專案結構

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ 先決條件

- 安裝 Raspberry Pi OS（或相容 Debian 系映像）的 Raspberry Pi
- 可用的 WiFi 介面 `wlan0`
- 可用的乙太網路上行介面 `eth0`
- `sudo` 權限
- 可供 `apt-get install` 使用的網際網路連線

假設：腳本使用傳統介面命名（`wlan0`、`eth0`）。若你的系統使用不同名稱，請依實際情況更新腳本/設定。

## 📦 安裝

複製此儲存庫：

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
```

賦予腳本執行權限：

```bash
sudo chmod +x setup.sh
```

## 🚀 使用方式

執行設定腳本：

```bash
./setup.sh
```

接著：

1. 輸入網路名稱（SSID）
2. 輸入密碼
3. 確認密碼
4. 重新開機

## 📋 操作步驟

（保留原始流程作為標準基準。）

1. 使用 git 複製儲存庫  
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. 切換到儲存庫目錄  
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. 賦予腳本執行權限  
   `sudo chmod +x setup.sh`
4. 執行腳本  
   `./setup.sh`
5. 輸入網路名稱、密碼與密碼確認
6. 重新開機！

## ⚙️ 設定

腳本會寫入/更新以下檔案：

| 檔案 | 用途 |
| --- | --- |
| `/etc/dhcpcd.conf` | 新增 `wlan0` 的靜態 AP 設定 |
| `/etc/sysctl.d/routed-ap.conf` | 設定 `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | AP DHCP/DNS 設定（原始檔會移至 `/etc/dnsmasq.conf.orig`） |
| `/etc/hostapd/hostapd.conf` | SSID、安全性與無線電參數設定 |

### 使用的關鍵 hostapd 參數

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

若要在設定後自訂無線電/安全性預設值，請編輯：

- `/etc/hostapd/hostapd.conf`

## 📝 注意事項

1. 這會建立一個在頻道 2 上運作的 2.4GHz WiFi 網路
2. 國家代碼設為美國
3. 若要變更這些設定，請編輯 `/etc/hostapd/hostapd.conf`
4. 儲存庫中已有 `i18n/`；其中連結的多語 README 會隨時間新增/更新。

## 🔍 範例

將裝置連線到你設定的 SSID 後，可在 Pi 上驗證路由：

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

成功執行後，預期的 AP 端存取資訊：

- Raspberry Pi AP IP：`10.20.1.1`
- 本機 DNS 名稱：`rpi.ap`

## 🛠️ 開發備註

- 主要邏輯位於 `setup.sh`
- 此儲存庫目前沒有測試套件、CI 流程或套件清單
- 腳本採用命令式操作，會直接寫入系統設定
- 重複執行時，某些檔案可能會附加重複行（特別是 `/etc/dhcpcd.conf`）

## 🧰 疑難排解

| 問題 | 檢查 / 修正 |
| --- | --- |
| `Passwords do not match` | 重新執行 `./setup.sh`，並輸入一致的值 |
| 設定後看不到 AP | 重新啟動 Pi，然後檢查 `systemctl status hostapd` |
| WiFi 用戶端無法上網 | 確認乙太網路上游可上網、用 `sudo iptables -t nat -S` 驗證 NAT 規則、用 `cat /etc/sysctl.d/routed-ap.conf` 驗證轉送設定 |
| 服務啟動異常 | `sudo journalctl -u hostapd -b` 與 `sudo journalctl -u dnsmasq -b` |

## 🧭 路線圖

- 新增可重複安全執行（idempotent）的設定邏輯
- 新增解除安裝/重置腳本以還原先前設定
- 新增介面覆寫支援（非 `wlan0`/`eth0` 系統）
- 新增自動化驗證檢查與 CI
- 在 `i18n/` 補齊已完成的翻譯

## 🤝 貢獻

歡迎提交 Issue 與 Pull Request。

貢獻時請注意：

- 讓指令與文件內容與實際腳本行為一致
- 涉及網路邏輯變更時，請在實體 Raspberry Pi 硬體上測試
- 記錄任何新預設值（頻道、子網路、服務）

## 📄 授權

本專案採用 GNU General Public License v3.0 授權。

請參閱 [LICENSE](LICENSE)。
