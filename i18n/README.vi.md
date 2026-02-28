[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)

Tập lệnh thiết lập Điểm truy cập WiFi tự động cho Raspberry Pi. Nó biến Pi của bạn thành một điểm truy cập chuyển tiếp lưu lượng qua cổng ethernet. Nói ngắn gọn, tập lệnh này biến Raspberry Pi của bạn thành bộ định tuyến WiFi.

## 🌐 Tổng Quan

Kho lưu trữ này cung cấp một tập lệnh shell tương tác duy nhất, `setup.sh`, để cấu hình Raspberry Pi thành điểm truy cập không dây định tuyến:

- Thiết bị khách WiFi kết nối vào `wlan0`
- Raspberry Pi chuyển tiếp lưu lượng sang `eth0`
- NAT được cấu hình bằng `iptables`
- DHCP/DNS được cung cấp bởi `dnsmasq`
- Dịch vụ AP được cung cấp bởi `hostapd`

Giá trị mạng AP mặc định do tập lệnh tạo:

| Thiết lập | Giá trị |
| --- | --- |
| IP giao diện AP | `10.20.1.1/24` |
| Dải DHCP | `10.20.1.5` - `10.20.1.100` |
| Tên cục bộ AP | `rpi.ap` |
| Chế độ WiFi | 2.4 GHz (`hw_mode=g`) |
| Kênh | `2` |
| Mã quốc gia | `US` |

## ✨ Tính Năng

- Thiết lập tương tác (yêu cầu nhập SSID và mật khẩu WPA2)
- Tự động cài đặt gói:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- Cấp phát mạng AP trên `wlan0`
- Bật chuyển tiếp IPv4
- Quy tắc NAT bền vững cho đường lên qua `eth0`
- Tự động tạo cấu hình `hostapd` và `dnsmasq`
- Bật `hostapd.service` khi khởi động

## 🗂️ Cấu Trúc Dự Án

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ Điều Kiện Tiên Quyết

- Raspberry Pi chạy Raspberry Pi OS (hoặc bản dựng tương thích dựa trên Debian)
- Bộ điều hợp WiFi hoạt động dưới tên `wlan0`
- Có đường uplink Ethernet dưới tên `eth0`
- Quyền `sudo`
- Truy cập Internet để chạy `apt-get install`

Giả định: Tập lệnh sử dụng tên giao diện cổ điển (`wlan0`, `eth0`). Nếu hệ thống của bạn dùng tên khác, hãy cập nhật tập lệnh/cấu hình tương ứng.

## 📦 Cài Đặt

Sao chép kho lưu trữ:

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
```

Cấp quyền thực thi cho tập lệnh:

```bash
sudo chmod +x setup.sh
```

## 🚀 Cách Sử Dụng

Chạy tập lệnh thiết lập:

```bash
./setup.sh
```

Sau đó:

1. Nhập tên mạng (SSID)
2. Nhập mật khẩu
3. Xác nhận mật khẩu
4. Khởi động lại

## 📋 Hướng Dẫn

(Quy trình gốc được giữ nguyên làm cơ sở chuẩn.)

1. Sao chép kho lưu trữ bằng git  
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. Chuyển vào thư mục kho lưu trữ  
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. Cấp quyền thực thi cho tập lệnh  
   `sudo chmod +x setup.sh`
4. Chạy tập lệnh  
   `./setup.sh`
5. Nhập tên mạng, mật khẩu và xác nhận mật khẩu
6. Khởi động lại!

## ⚙️ Cấu Hình

Tập lệnh ghi/cập nhật các tệp sau:

| Tệp | Mục đích |
| --- | --- |
| `/etc/dhcpcd.conf` | Thêm cấu hình AP tĩnh cho `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | Đặt `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | Cấu hình DHCP/DNS cho AP (bản gốc được chuyển sang `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | Thiết lập SSID, bảo mật và radio |

### Các thiết lập hostapd chính được sử dụng

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

Để tùy chỉnh mặc định radio/bảo mật sau khi thiết lập, hãy chỉnh sửa:

- `/etc/hostapd/hostapd.conf`

## 📝 Ghi Chú

1. Điều này sẽ tạo một mạng WiFi 2.4GHz trên kênh 2
2. Mã quốc gia được đặt là Hoa Kỳ
3. Để thay đổi các thiết lập này, hãy chỉnh sửa tệp `/etc/hostapd/hostapd.conf`
4. `i18n/` tồn tại trong kho lưu trữ; các README ngôn ngữ được liên kết có thể được thêm/cập nhật tại đó theo thời gian.

## 🔍 Ví Dụ

Kết nối một thiết bị vào SSID đã cấu hình, sau đó xác minh định tuyến từ Pi:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

Chi tiết truy cập phía AP được mong đợi sau khi chạy thành công:

- IP AP của Raspberry Pi: `10.20.1.1`
- Tên DNS cục bộ: `rpi.ap`

## 🛠️ Ghi Chú Phát Triển

- Logic chính nằm trong `setup.sh`
- Kho lưu trữ này hiện chưa có bộ kiểm thử, pipeline CI hoặc manifest gói
- Tập lệnh hoạt động theo kiểu mệnh lệnh (ghi trực tiếp vào cấu hình hệ thống)
- Chạy lại có thể thêm các dòng trùng lặp trong một số tệp (đặc biệt là `/etc/dhcpcd.conf`)

## 🧰 Khắc Phục Sự Cố

| Sự cố | Kiểm tra / Cách khắc phục |
| --- | --- |
| `Passwords do not match` | Chạy lại `./setup.sh` và nhập lại các giá trị khớp nhau |
| AP không hiển thị sau khi thiết lập | Khởi động lại Pi, sau đó kiểm tra `systemctl status hostapd` |
| Thiết bị khách WiFi không có Internet | Xác nhận đường uplink Ethernet có Internet, kiểm tra quy tắc NAT bằng `sudo iptables -t nat -S`, kiểm tra chuyển tiếp bằng `cat /etc/sysctl.d/routed-ap.conf` |
| Vấn đề khởi động dịch vụ | `sudo journalctl -u hostapd -b` và `sudo journalctl -u dnsmasq -b` |

## 🧭 Lộ Trình

- Thêm logic thiết lập idempotent (chạy lại an toàn)
- Thêm tập lệnh gỡ cài đặt/đặt lại để khôi phục cấu hình trước đó
- Thêm hỗ trợ ghi đè giao diện (hệ thống không dùng `wlan0`/`eth0`)
- Thêm kiểm tra xác thực tự động và CI
- Thêm các bản dịch hoàn chỉnh trong `i18n/`

## 🤝 Đóng Góp

Hoan nghênh issue và pull request.

Khi đóng góp:

- Giữ cho lệnh và tài liệu khớp với hành vi thực tế của tập lệnh
- Kiểm thử trên phần cứng Raspberry Pi thực tế khi thay đổi logic mạng
- Ghi lại mọi mặc định mới (kênh, dải mạng con, dịch vụ)

## 📄 Giấy Phép

Dự án này được cấp phép theo GNU General Public License v3.0.

Xem [LICENSE](LICENSE).
