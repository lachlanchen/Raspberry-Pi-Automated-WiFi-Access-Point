[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


[![LazyingArt banner](https://github.com/lachlanchen/lachlanchen/raw/main/figs/banner.png)](https://github.com/lachlanchen/lachlanchen/blob/main/figs/banner.png)

# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)
![Config: NAT Routed AP](https://img.shields.io/badge/Config-NAT%20Routed%20AP-2b6cb0)

سكربت إعداد نقطة وصول Wi‑Fi تلقائي لجهاز Raspberry Pi. يضبط `wlan0` كنقطة وصول AP ويوجه حركة المرور من العملاء عبر `eth0` باستخدام NAT.

> حوّل Raspberry Pi إلى نقطة وصول Wi‑Fi مرشّحة وموثوقة بنقطة تحكم واحدة.

## 🧩 لقطة سريعة

| المجال | التفاصيل |
| --- | --- |
| نطاق المشروع | أتمتة نقطة وصول لـ Raspberry Pi مبنية على Raspbian/Debian |
| شبكة AP الافتراضية | `10.20.1.1/24` |
| نطاق إعطاء عناوين DHCP | `10.20.1.5` - `10.20.1.100` |
| إعدادات الراديو الافتراضية | `hw_mode=g`، القناة `2`، البلد `US` |
| الملفات التي يُعدّلها السكربت | `/etc/dhcpcd.conf`, `/etc/hostapd/hostapd.conf`, `/etc/dnsmasq.conf`, `/etc/sysctl.d/routed-ap.conf` |

---

## 🌐 النظرة العامة

يحتوي هذا المستودع على سكربت تفاعلي واحد `setup.sh` يحول Raspberry Pi إلى راوتر Wi‑Fi. يقوم بتثبيت وتكوين الحزم والخدمات اللازمة لنقطة وصول موجهة:

- **DHCP/DNS** عبر `dnsmasq`
- **خدمة AP** عبر `hostapd`
- **NAT وتوجيه الحزم بشكل دائم** عبر `iptables`/`netfilter-persistent`

إعدادات شبكة الوصول الافتراضية التي ينشئها السكربت:

| الإعداد | القيمة |
| --- | --- |
| IP واجهة AP | `10.20.1.1/24` |
| نطاق DHCP | `10.20.1.5` - `10.20.1.100` |
| اسم محلي لـ AP | `rpi.ap` |
| وضع Wi‑Fi | 2.4 جيجا هرتز (`hw_mode=g`) |
| القناة | `2` |
| رمز الدولة | `US` |

## ✨ المزايا

- يطلب بشكل تفاعلي إدخال SSID وعبارة مرور WPA2 (مع تأكيد)
- يثبّت الاعتماديات الأساسية:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- يضبط شبكة AP ثابتة على `wlan0`
- يفعّل تمرير IPv4 في `/etc/sysctl.d/routed-ap.conf`
- يضيف قاعدة NAT/masquerade دائمة لحركة المرور الخارجة عبر `eth0`
- ينشئ تلقائيًا `/etc/dnsmasq.conf` و`/etc/hostapd/hostapd.conf`
- يفعّل ويبدأ `hostapd.service` عند الإقلاع

## 🗂️ هيكل المشروع

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ المتطلبات الأساسية

- Raspberry Pi يعمل بنظام Raspberry Pi OS (أو صورة Debian متوافقة)
- موصل واي-فاي يعمل يظهر كـ `wlan0`
- وصلة إنترنت سلكية على `eth0`
- اتصال إنترنت لتثبيت الحزم
- صلاحيات `sudo`

المبدأ: يفترض السكربت استخدام أسماء الواجهات التقليدية (`wlan0` و`eth0`). الأسماء غير القياسية تحتاج تعديلًا يدويًا بعد الإعداد.

## 📦 التثبيت

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
sudo chmod +x setup.sh
```

---

## 🚀 الاستخدام

شغّل سكربت الإعداد:

```bash
./setup.sh
```

تسلسل التشغيل التفاعلي النموذجي:

1. أدخل SSID
2. أدخل كلمة مرور WPA2
3. أكّد كلمة المرور
4. أعد تشغيل Pi

## 📋 التعليمات

1. استنساخ هذا المستودع:
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. تغيير المجلد:
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. جعل السكربت قابلًا للتنفيذ:
   `sudo chmod +x setup.sh`
4. تشغيل الإعداد:
   `./setup.sh`
5. أدخل SSID وكلمة المرور وتأكيد كلمة المرور
6. أعد تشغيل Raspberry Pi

## ⚙️ الإعداد

يقوم السكربت بكتابة/تحديث هذه الملفات:

| الملف | الغرض |
| --- | --- |
| `/etc/dhcpcd.conf` | يضيف إعداد AP ثابتًا لـ `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | يفعّل تمرير IPv4 عبر `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | يضبط DHCP/DNS لعملاء AP (ويُنقل النسخ الاحتياطي إلى `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | يخزن إعدادات SSID والراديو والأمان الخاصة بـ WLAN |

إعدادات `hostapd` الأساسية:

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

لتخصيص القيم الافتراضية، حرّر:

- `/etc/hostapd/hostapd.conf`
- `/etc/dnsmasq.conf` (عند الحاجة لسلوك DNS/DHCP)
- `/etc/dhcpcd.conf` (للشبكة الفرعية/العناوين)

## 📝 ملاحظات

1. هذا السكربت ينشئ SSID على تردد 2.4GHz بالقناة `2` افتراضياً.
2. رمز الدولة افتراضيًا هو `US`.
3. يحتوي المجلد `i18n/` على نسخ README مترجمة مرتبطة من محدد اللغة في الأعلى.

## 🔍 أمثلة

تحقق من إعدادات AP والـ forwarding:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

تفاصيل متوقعة من جهة AP بعد الإعداد الناجح:

- IP نقطة الوصول: `10.20.1.1`
- اسم DNS المحلي لـ AP: `rpi.ap`

## 🛠️ ملاحظات التطوير

- التنفيذ الأساسي مركّز في `setup.sh`.
- لا توجد اختبارات أو CI أو ملف حزمة في هذا المستودع.
- يقوم السكربت بإجراء تغييرات مباشرة على ملفات النظام وليس تام التكرار في جميع الملفات (فقد تتكرر سطور في `/etc/dhcpcd.conf` بعد تشغيلات متكررة).

---

## 🧰 استكشاف الأخطاء وإصلاحها

| المشكلة | الفحص / الإصلاح |
| --- | --- |
| `Passwords do not match` | أعد تشغيل `./setup.sh` وأعد إدخال قيم متطابقة |
| AP غير ظاهر | أعد التشغيل وافحص `systemctl status hostapd` |
| لا يوجد إنترنت على أجهزة العملاء | تأكد من وصلة الإيثرنت، وراجع NAT باستخدام `sudo iptables -t nat -S`، وتأكد من إعدادات التمرير في `/etc/sysctl.d/routed-ap.conf` |
| مشاكل في بدء الخدمة | نفّذ `sudo journalctl -u hostapd -b` و`sudo journalctl -u dnsmasq -b` |

## 🧭 خارطة الطريق

- جعل الإعداد أكثر idempotent لإعادة التشغيل الآمن.
- إضافة مسار uninstall/reset لاستعادة حالة الشبكات السابقة.
- إضافة دعم لتجاوز الواجهات في الأنظمة غير `wlan0`/`eth0`.
- إضافة فحوصات smoke آلية وCI اختياري.
- إضافة وصيانة الترجمات ضمن `i18n/`.

## 🤝 المساهمة

المساهمات من خلال الإبلاغ عن المشكلات (Issues) أو طلبات السحب (Pull Requests) مرحّب بها.

فحوصات المساهمة الموصى بها:

- حافظ على توحيد الوثائق والأوامر مع سلوك السكربت الفعلي.
- اختبر تغييرات الشبكة على أجهزة Raspberry Pi فعلية.
- وثّق أي تغييرات على القيم الافتراضية والآثار الجانبية.

## 📄 الترخيص

هذا المشروع مرخّص تحت رخصة GNU General Public License v3.0.

انظر ملف [LICENSE](LICENSE).


## ❤️ Support

| Donate | PayPal | Stripe |
| --- | --- | --- |
| [![Donate](https://camo.githubusercontent.com/24a4914f0b42c6f435f9e101621f1e52535b02c225764b2f6cc99416926004b7/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f6e6174652d4c617a79696e674172742d3045413545393f7374796c653d666f722d7468652d6261646765266c6f676f3d6b6f2d6669266c6f676f436f6c6f723d7768697465)](https://chat.lazying.art/donate) | [![PayPal](https://camo.githubusercontent.com/d0f57e8b016517a4b06961b24d0ca87d62fdba16e18bbdb6aba28e978dc0ea21/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50617950616c2d526f6e677a686f754368656e2d3030343537433f7374796c653d666f722d7468652d6261646765266c6f676f3d70617970616c266c6f676f436f6c6f723d7768697465)](https://paypal.me/RongzhouChen) | [![Stripe](https://camo.githubusercontent.com/1152dfe04b6943afe3a8d2953676749603fb9f95e24088c92c97a01a897b4942/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f5374726970652d446f6e6174652d3633354246463f7374796c653d666f722d7468652d6261646765266c6f676f3d737472697065266c6f676f436f6c6f723d7768697465)](https://buy.stripe.com/aFadR8gIaflgfQV6T4fw400) |
