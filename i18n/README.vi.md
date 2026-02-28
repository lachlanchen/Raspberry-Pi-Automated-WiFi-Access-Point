[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)
![Config: NAT Routed AP](https://img.shields.io/badge/Config-NAT%20Routed%20AP-2b6cb0)

Một script thiết lập điểm truy cập Wi‑Fi tự động cho Raspberry Pi. Script này cấu hình `wlan0` làm AP và định tuyến lưu lượng của client qua `eth0` bằng NAT.

> Chỉ với một script hướng dẫn, biến Raspberry Pi thành một AP định tuyến Wi‑Fi đáng tin cậy.

## 🧩 Snapshot

| Khu vực | Chi tiết |
| --- | --- |
| Phạm vi dự án | Tự động hóa điểm truy cập Raspberry Pi dựa trên Raspbian/Debian |
| Mạng con AP mặc định | `10.20.1.1/24` |
| Khoảng cấp DHCP | `10.20.1.5` - `10.20.1.100` |
| Mặc định radio Wi‑Fi | `hw_mode=g`, kênh `2`, mã quốc gia `US` |
| Các tệp được script sinh | `/etc/dhcpcd.conf`, `/etc/hostapd/hostapd.conf`, `/etc/dnsmasq.conf`, `/etc/sysctl.d/routed-ap.conf` |

---

## 🌐 Tổng quan

Kho lưu trữ này chứa một script shell tương tác duy nhất là `setup.sh`, giúp biến Raspberry Pi thành một router Wi‑Fi. Script sẽ cài đặt và cấu hình các gói dịch vụ cần thiết cho một điểm truy cập có định tuyến:

- **DHCP/DNS** qua `dnsmasq`
- **Dịch vụ AP** qua `hostapd`
- **NAT và forward gói tin bền vững** qua `iptables`/`netfilter-persistent`

Các giá trị mạng truy cập mặc định do script tạo:

| Thiết lập | Giá trị |
| --- | --- |
| IP giao diện AP | `10.20.1.1/24` |
| Dải DHCP | `10.20.1.5` - `10.20.1.100` |
| Tên nội bộ AP | `rpi.ap` |
| Chế độ Wi‑Fi | 2.4 GHz (`hw_mode=g`) |
| Kênh | `2` |
| Mã quốc gia | `US` |

## ✨ Tính năng

- Thiết lập tương tác yêu cầu nhập SSID và mật khẩu WPA2 (có xác nhận lại)
- Cài đặt các phụ thuộc chính:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- Cấu hình mạng AP tĩnh trên `wlan0`
- Bật chuyển tiếp IPv4 trong `/etc/sysctl.d/routed-ap.conf`
- Thêm quy tắc NAT/masquerade bền vững cho lưu lượng ra `eth0`
- Tự động sinh `/etc/dnsmasq.conf` và `/etc/hostapd/hostapd.conf`
- Bật và khởi động `hostapd.service` khi khởi động máy

## 🗂️ Cấu trúc dự án

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ Yêu cầu trước

- Raspberry Pi chạy Raspberry Pi OS (hoặc bản Debian-based tương thích)
- Một bộ phát Wi‑Fi hoạt động với tên `wlan0`
- Một kết nối lên internet trên `eth0`
- Có kết nối internet để cài đặt gói
- Quyền `sudo`

Giả định: hành vi của script dựa trên tên giao diện chuẩn (`wlan0` và `eth0`). Tên không chuẩn cần chỉnh sửa thủ công sau khi cài đặt.

## 📦 Cài đặt

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
sudo chmod +x setup.sh
```

---

## 🚀 Cách dùng

Chạy script cài đặt:

```bash
./setup.sh
```

Luồng tương tác phổ biến:

1. Nhập SSID
2. Nhập mật khẩu WPA2
3. Xác nhận lại mật khẩu
4. Khởi động lại Pi

## 📋 Hướng dẫn

1. Clone kho lưu trữ này:
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. Chuyển vào thư mục:
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. Cấp quyền thực thi cho script:
   `sudo chmod +x setup.sh`
4. Chạy script:
   `./setup.sh`
5. Nhập SSID, mật khẩu, và xác nhận mật khẩu
6. Khởi động lại Raspberry Pi

## ⚙️ Cấu hình

Script ghi/cập nhật các tệp hệ thống sau:

| Tệp | Mục đích |
| --- | --- |
| `/etc/dhcpcd.conf` | Thêm cấu hình AP tĩnh cho `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | Bật chuyển tiếp IPv4 qua `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | Cấu hình DHCP/DNS cho client AP (bản sao lưu chuyển vào `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | Lưu SSID và cài đặt WLAN radio/bảo mật |

Mặc định chính cho `hostapd`:

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

Để chỉnh sửa mặc định:

- `/etc/hostapd/hostapd.conf`
- `/etc/dnsmasq.conf` (nếu cần thay đổi hành vi DNS/DHCP)
- `/etc/dhcpcd.conf` (cho subnet/địa chỉ)

## 📝 Ghi chú

1. Script tạo SSID Wi‑Fi băng tần 2.4GHz ở kênh `2` theo mặc định.
2. Mã quốc gia mặc định là `US`.
3. `i18n/` chứa các phiên bản README dịch và được liên kết từ bộ chọn ngôn ngữ đầu trang.

## 🔍 Ví dụ

Kiểm tra AP và cấu hình forwarding:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Chi tiết AP sau khi cài đặt thành công:

- IP AP: `10.20.1.1`
- Tên DNS nội bộ AP: `rpi.ap`

## 🛠️ Ghi chú phát triển

- Cốt lõi triển khai tập trung trong `setup.sh`.
- Không có test suite, CI hoặc file manifest gói trong kho này.
- Script thực hiện ghi cấu hình hệ thống trực tiếp và chưa hoàn toàn idempotent ở mọi tệp (ví dụ `/etc/dhcpcd.conf` có thể tích lũy các dòng trùng lặp khi chạy lặp lại).

---

## 🧰 Khắc phục sự cố

| Vấn đề | Kiểm tra / Sửa |
| --- | --- |
| `Passwords do not match` | Chạy lại `./setup.sh` và nhập lại đúng cặp giá trị |
| AP không hiển thị | Khởi động lại và kiểm tra `systemctl status hostapd` |
| Thiết bị client không có internet | Kiểm tra uplink Ethernet, xem quy tắc NAT bằng `sudo iptables -t nat -S`, xác nhận cấu hình chuyển tiếp trong `/etc/sysctl.d/routed-ap.conf` |
| Dịch vụ khởi động lỗi | Chạy `sudo journalctl -u hostapd -b` và `sudo journalctl -u dnsmasq -b` |

## 🧭 Lộ trình

- Làm cho setup idempotent để chạy lại an toàn hơn.
- Thêm đường dẫn gỡ cài đặt/phục hồi trạng thái mạng trước đó.
- Hỗ trợ ghi đè interface cho hệ thống không dùng `wlan0`/`eth0`.
- Thêm smoke check tự động và CI tùy chọn.
- Thêm và duy trì bản dịch trong `i18n/`.

## 🤝 Đóng góp

Tất cả issue và pull request đều được chào đón.

Các kiểm tra khi đóng góp được khuyến nghị:

- Giữ tài liệu và lệnh đồng nhất với hành vi thật của script.
- Kiểm thử thay đổi mạng trên phần cứng Raspberry Pi thực tế.
- Ghi chú mọi thay đổi giá trị mặc định và tác động phụ.

## 📄 Giấy phép

Dự án này được cấp phép theo Giấy phép Công cộng GNU phiên bản 3.0.

Xem [LICENSE](LICENSE).


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
