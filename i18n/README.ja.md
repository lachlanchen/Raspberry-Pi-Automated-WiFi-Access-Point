[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)
![Config: NAT Routed AP](https://img.shields.io/badge/Config-NAT%20Routed%20AP-2b6cb0)

Raspberry Pi 向けの自動化された Wi‑Fi アクセスポイント設定スクリプトです。`wlan0` を AP として設定し、`eth0` を介してクライアント通信を NAT ルーティングします。

> 一つの対話式スクリプトで、Raspberry Pi を安定したルーティング対応 Wi‑Fi AP に変える。

## 🧩 Snapshot

| 項目 | 詳細 |
| --- | --- |
| プロジェクト範囲 | Raspbian/Debian ベースの Raspberry Pi アクセスポイント自動化 |
| AP の既定サブネット | `10.20.1.1/24` |
| DHCP リース範囲 | `10.20.1.5` - `10.20.1.100` |
| Wi‑Fi 無線の既定値 | `hw_mode=g`、チャンネル `2`、国コード `US` |
| 設定対象ファイル | `/etc/dhcpcd.conf`、`/etc/hostapd/hostapd.conf`、`/etc/dnsmasq.conf`、`/etc/sysctl.d/routed-ap.conf` |

---

## 🌐 Overview

このリポジトリには、Raspberry Pi を Wi‑Fi ルーターへ変換する単一の対話式シェルスクリプト `setup.sh` が含まれています。ルーティング対応アクセスポイントに必要なパッケージやサービスをインストール・設定します。

- `dnsmasq` による **DHCP/DNS**
- `hostapd` による **AP デーモン**
- `iptables` / `netfilter-persistent` による **永続的な NAT とパケット転送**

スクリプトが作成する AP 側の既定値:

| 設定 | 値 |
| --- | --- |
| AP インターフェース IP | `10.20.1.1/24` |
| DHCP 範囲 | `10.20.1.5` - `10.20.1.100` |
| AP ローカル名 | `rpi.ap` |
| Wi‑Fi モード | 2.4 GHz (`hw_mode=g`) |
| チャネル | `2` |
| 国コード | `US` |

## ✨ Features

- SSID と WPA2 パスフレーズ（確認入力付き）を対話形式で入力
- 必要な依存関係をインストール:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- `wlan0` に対して固定 AP ネットワークを設定
- `/etc/sysctl.d/routed-ap.conf` で IPv4 転送を有効化
- `eth0` 経由の外向き通信のために永続的な NAT/マスカレードルールを追加
- `/etc/dnsmasq.conf` と `/etc/hostapd/hostapd.conf` を自動生成
- 起動時に `hostapd.service` を有効化して起動

## 🗂️ Project Structure

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ Prerequisites

- Raspberry Pi OS（または互換性のある Debian ベース）を実行する Raspberry Pi
- `wlan0` として認識される Wi‑Fi アダプタ
- `eth0` の有線アップリンク
- パッケージインストールのためのインターネット接続
- `sudo` 権限

前提: スクリプトの挙動は従来のインターフェース名（`wlan0` と `eth0`）を想定しています。非標準名の環境では、セットアップ後に手動で調整が必要です。

## 📦 Installation

```bash

git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
sudo chmod +x setup.sh
```

---

## 🚀 Usage

セットアップスクリプトを実行します:

```bash
./setup.sh
```

一般的な対話フロー:

1. SSID を入力
2. WPA2 パスフレーズを入力
3. パスフレーズ確認を入力
4. Raspberry Pi を再起動

## 📋 Instructions

1. リポジトリをクローン:
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. ディレクトリを移動:
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. スクリプトに実行権限を付与:
   `sudo chmod +x setup.sh`
4. セットアップを実行:
   `./setup.sh`
5. SSID、パスワード、パスワード確認を入力
6. Raspberry Pi を再起動

## ⚙️ Configuration

このスクリプトは以下のシステムファイルを書き込み/更新します:

| ファイル | 目的 |
| --- | --- |
| `/etc/dhcpcd.conf` | `wlan0` の固定 AP 設定を追加 |
| `/etc/sysctl.d/routed-ap.conf` | `net.ipv4.ip_forward=1` で IPv4 転送を有効化 |
| `/etc/dnsmasq.conf` | AP クライアント向け DHCP/DNS を設定（バックアップは `/etc/dnsmasq.conf.orig`） |
| `/etc/hostapd/hostapd.conf` | SSID と WLAN の無線/セキュリティ設定を保存 |

`hostapd` の主要既定値:

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

既定値をカスタマイズする場合は、次のファイルを編集してください:

- `/etc/hostapd/hostapd.conf`
- `/etc/dnsmasq.conf`（必要に応じて DNS/DHCP 挙動を調整）
- `/etc/dhcpcd.conf`（サブネット/アドレスの変更）

## 📝 Notes

1. このスクリプトは既定で 2.4GHz の SSID をチャネル `2` で作成します。
2. 国コードは既定で `US` です。
3. `i18n/` には上部の言語セレクターから参照される README の翻訳版があります。

## 🔍 Examples

AP と転送設定を確認:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

正常にセットアップできた場合の想定:

- AP 側 IP: `10.20.1.1`
- AP ローカル DNS 名: `rpi.ap`

## 🛠️ Development Notes

- 実装の中心は `setup.sh` にあります。
- このリポジトリにはテストスイート、CI、またはパッケージマニフェストは含まれていません。
- スクリプトはシステム設定を直接書き換えるため、すべてのファイルで完全な冪等性は担保されていません（例: `/etc/dhcpcd.conf` では再実行時に重複行が蓄積する可能性があります）。

---

## 🧰 Troubleshooting

| 問題 | 確認・対処 |
| --- | --- |
| `Passwords do not match` | `./setup.sh` を再実行して一致する値を再入力 |
| AP が見つからない | 再起動して `systemctl status hostapd` を確認 |
| クライアント端末でインターネットに出ない | 有線アップリンクを確認し、`sudo iptables -t nat -S` で NAT を確認、`/etc/sysctl.d/routed-ap.conf` の転送設定を確認 |
| サービス起動に問題 | `sudo journalctl -u hostapd -b` と `sudo journalctl -u dnsmasq -b` を実行 |

## 🧭 Roadmap

- 再実行時の安全性を高めるため、冪等なセットアップを実装
- 以前のネットワーク状態を復元するアンインストール/リセット経路を追加
- `wlan0` / `eth0` 以外のインターフェース向けに、インターフェース上書き対応を追加
- 自動チェックと任意の CI を追加
- `i18n/` 配下の翻訳を追加・維持

## 🤝 Contributing

Issue と Pull Request を歓迎します。

推奨される確認項目:

- ドキュメントとコマンドをスクリプトの実際の動作に合わせる
- Raspberry Pi 実機でネットワーク変更をテストする
- 変更したデフォルト値と影響範囲を必ず明記する

## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |

## 📄 License

このプロジェクトは GNU General Public License v3.0 の下でライセンスされています。

詳細は [LICENSE](../LICENSE) を参照してください。
