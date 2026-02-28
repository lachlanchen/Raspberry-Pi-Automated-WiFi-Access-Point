[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)

Raspberry Pi向けの、自動化されたWiFiアクセスポイント構築スクリプトです。Piをアクセスポイント化し、トラフィックを有線LANポート経由で転送します。つまり、このスクリプトによりRaspberry PiをWiFiルーターとして動作させられます。

## 🌐 概要

このリポジトリには、Raspberry Piをルーティング対応の無線アクセスポイントとして構成する単一の対話型シェルスクリプト `setup.sh` が含まれています。

- WiFiクライアントは `wlan0` に接続
- Raspberry Piはトラフィックを `eth0` へ転送
- NATは `iptables` で構成
- DHCP/DNSは `dnsmasq` が提供
- APサービスは `hostapd` が提供

スクリプトが作成するAPネットワークのデフォルト値:

| 設定 | 値 |
| --- | --- |
| AP interface IP | `10.20.1.1/24` |
| DHCP range | `10.20.1.5` - `10.20.1.100` |
| AP local name | `rpi.ap` |
| WiFi mode | 2.4 GHz (`hw_mode=g`) |
| Channel | `2` |
| Country code | `US` |

## ✨ 特長

- 対話型セットアップ（SSIDとWPA2パスフレーズを入力）
- パッケージを自動インストール:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- `wlan0` 上でAPネットワークをプロビジョニング
- IPv4転送を有効化
- `eth0` を上流にした永続NATルールを設定
- `hostapd` と `dnsmasq` の設定を自動生成
- 起動時実行のため `hostapd.service` を有効化

## 🗂️ プロジェクト構成

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ 前提条件

- Raspberry Pi OS（または互換性のあるDebian系イメージ）を搭載したRaspberry Pi
- `wlan0` として利用可能なWiFiアダプタ
- `eth0` として利用可能な有線アップリンク
- `sudo` 権限
- `apt-get install` を実行できるインターネット接続

前提: このスクリプトは従来のインターフェース名（`wlan0`、`eth0`）を使用します。システムで別名を使っている場合は、スクリプト/設定をそれに合わせて更新してください。

## 📦 インストール

リポジトリをクローン:

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
```

スクリプトに実行権限を付与:

```bash
sudo chmod +x setup.sh
```

## 🚀 使い方

セットアップスクリプトを実行:

```bash
./setup.sh
```

その後の流れ:

1. ネットワーク名（SSID）を入力
2. パスワードを入力
3. パスワード確認を入力
4. 再起動

## 📋 手順

（元のワークフローを正本として保持しています。）

1. gitでリポジトリをクローン  
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. リポジトリのディレクトリへ移動  
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. スクリプトに実行権限を付与  
   `sudo chmod +x setup.sh`
4. スクリプトを実行  
   `./setup.sh`
5. ネットワーク名、パスワード、パスワード確認を入力
6. 再起動！

## ⚙️ 設定

このスクリプトは以下のファイルを書き込み/更新します:

| File | Purpose |
| --- | --- |
| `/etc/dhcpcd.conf` | `wlan0` 向けの静的AP設定を追加 |
| `/etc/sysctl.d/routed-ap.conf` | `net.ipv4.ip_forward=1` を設定 |
| `/etc/dnsmasq.conf` | APのDHCP/DNS設定（元ファイルは `/etc/dnsmasq.conf.orig` に移動） |
| `/etc/hostapd/hostapd.conf` | SSID、セキュリティ、無線設定 |

### 使用される主な hostapd 設定

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

セットアップ後に無線/セキュリティのデフォルト値をカスタマイズする場合は、次を編集してください:

- `/etc/hostapd/hostapd.conf`

## 📝 注意事項

1. 2.4GHz・チャネル2のWiFiネットワークを作成します
2. 国コードは米国（United States）に設定されます
3. これらの設定を変更するには `/etc/hostapd/hostapd.conf` を編集してください
4. `i18n/` はリポジトリ内に存在します。各言語READMEへのリンク先は今後追加・更新される可能性があります。

## 🔍 例

設定したSSIDにデバイスを接続し、Pi側でルーティングを確認します:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

正常完了後に想定されるAP側アクセス情報:

- Raspberry Pi AP IP: `10.20.1.1`
- ローカルDNS名: `rpi.ap`

## 🛠️ 開発メモ

- 主要ロジックは `setup.sh` にあります
- 現時点でこのリポジトリにはテストスイート、CIパイプライン、パッケージマニフェストはありません
- スクリプトは実運用向けの命令型実装で、システム設定へ直接書き込みます
- 再実行すると一部ファイル（特に `/etc/dhcpcd.conf`）で重複行が追記される可能性があります

## 🧰 トラブルシューティング

| Issue | Check / Fix |
| --- | --- |
| `Passwords do not match` | `./setup.sh` を再実行し、同一の値を入力してください |
| セットアップ後にAPが見えない | Piを再起動し、`systemctl status hostapd` を確認 |
| WiFiクライアントでインターネットに接続できない | 有線アップリンクの接続を確認し、`sudo iptables -t nat -S` でNATルール、`cat /etc/sysctl.d/routed-ap.conf` でforwarding設定を確認 |
| サービス起動の問題 | `sudo journalctl -u hostapd -b` と `sudo journalctl -u dnsmasq -b` を確認 |

## 🧭 ロードマップ

- 再実行に安全な冪等セットアップロジックを追加
- 以前の設定へ戻せるアンインストール/リセットスクリプトを追加
- インターフェース名の上書き対応を追加（`wlan0`/`eth0` 以外の環境向け）
- 自動検証チェックとCIを追加
- `i18n/` 配下の翻訳を完成させる

## 🤝 コントリビュート

Issue と Pull Request を歓迎します。

コントリビュート時の指針:

- コマンドとドキュメントは、実際のスクリプト動作に一致させてください
- ネットワーク関連ロジックを変更する場合は、実機のRaspberry Piでテストしてください
- 新しいデフォルト値（チャネル、サブネット、サービス）を必ず文書化してください

## 📄 ライセンス

このプロジェクトは GNU General Public License v3.0 のもとでライセンスされています。

詳細は [LICENSE](../LICENSE) を参照してください。
