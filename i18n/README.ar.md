[English](../README.md) · [العربية](README.ar.md) · [Español](README.es.md) · [Français](README.fr.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Tiếng Việt](README.vi.md) · [中文 (简体)](README.zh-Hans.md) · [中文（繁體）](README.zh-Hant.md) · [Deutsch](README.de.md) · [Русский](README.ru.md)


# Raspberry-Pi-Automated-WiFi-Access-Point

![License: GPL-3.0](https://img.shields.io/badge/License-GPLv3-blue.svg)
![Platform: Raspberry Pi OS](https://img.shields.io/badge/Platform-Raspberry%20Pi%20OS-C51A4A)
![Network: WiFi AP + NAT](https://img.shields.io/badge/Network-AP%20%2B%20NAT-2ea44f)
![Shell: POSIX sh](https://img.shields.io/badge/Shell-POSIX%20sh-4EAA25)
![State: Scripted Setup](https://img.shields.io/badge/State-Interactive%20Script-orange)

سكريبت إعداد آلي لنقطة وصول WiFi على أجهزة Raspberry Pi. يحوّل جهاز Pi إلى نقطة وصول تقوم بتمرير حركة الشبكة عبر منفذ الإيثرنت. باختصار، هذا السكريبت يحوّل Raspberry Pi إلى موجّه WiFi.

## 🌐 نظرة عامة

يوفّر هذا المستودع سكريبت شِل تفاعليًا واحدًا، `setup.sh`، لإعداد Raspberry Pi كنقطة وصول لاسلكية موجَّهة:

- تتصل أجهزة WiFi العميلة عبر `wlan0`
- يقوم Raspberry Pi بتمرير الحركة إلى `eth0`
- يتم إعداد NAT باستخدام `iptables`
- يتم توفير DHCP/DNS عبر `dnsmasq`
- يتم توفير خدمة نقطة الوصول عبر `hostapd`

القيم الافتراضية لشبكة نقطة الوصول التي ينشئها السكريبت:

| الإعداد | القيمة |
| --- | --- |
| عنوان IP لواجهة نقطة الوصول | `10.20.1.1/24` |
| نطاق DHCP | `10.20.1.5` - `10.20.1.100` |
| الاسم المحلي لنقطة الوصول | `rpi.ap` |
| نمط WiFi | 2.4 GHz (`hw_mode=g`) |
| القناة | `2` |
| رمز الدولة | `US` |

## ✨ الميزات

- إعداد تفاعلي (يطلب SSID وعبارة مرور WPA2)
- تثبيت تلقائي للحزم:
  - `dnsmasq`
  - `hostapd`
  - `netfilter-persistent`
  - `iptables-persistent`
- تهيئة شبكة نقطة الوصول على `wlan0`
- تفعيل توجيه IPv4
- قاعدة NAT دائمة للاتصال الصاعد على `eth0`
- توليد تلقائي لإعدادات `hostapd` و`dnsmasq`
- تفعيل `hostapd.service` عند الإقلاع

## 🗂️ بنية المشروع

```text
Raspberry-Pi-Automated-WiFi-Access-Point/
├── README.md
├── setup.sh
├── LICENSE
└── i18n/
```

## ✅ المتطلبات المسبقة

- Raspberry Pi يعمل بنظام Raspberry Pi OS (أو توزيعة متوافقة مبنية على Debian)
- محول WiFi فعّال كواجهة `wlan0`
- وصلة إيثرنت صاعدة متاحة كواجهة `eth0`
- صلاحيات `sudo`
- اتصال إنترنت لتشغيل `apt-get install`

افتراض: يستخدم السكريبت أسماء الواجهات التقليدية (`wlan0`, `eth0`). إذا كان نظامك يستخدم أسماء مختلفة، حدّث السكريبت/الإعدادات وفقًا لذلك.

## 📦 التثبيت

استنسخ المستودع:

```bash
git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point
cd Raspberry-Pi-Automated-WiFi-Access-Point
```

اجعل السكريبت قابلًا للتنفيذ:

```bash
sudo chmod +x setup.sh
```

## 🚀 الاستخدام

شغّل سكريبت الإعداد:

```bash
./setup.sh
```

ثم:

1. أدخل اسم الشبكة (SSID)
2. أدخل كلمة المرور
3. أكّد كلمة المرور
4. أعد التشغيل

## 📋 التعليمات

(تم الحفاظ على سير العمل الأصلي كمرجع أساسي.)

1. استنسخ المستودع باستخدام git  
   `git clone https://github.com/arm358/Raspberry-Pi-Automated-WiFi-Access-Point`
2. انتقل إلى مجلد المستودع  
   `cd Raspberry-Pi-Automated-WiFi-Access-Point`
3. اجعل السكريبت قابلًا للتنفيذ  
   `sudo chmod +x setup.sh`
4. شغّل السكريبت  
   `./setup.sh`
5. أدخل اسم الشبكة وكلمة المرور وتأكيد كلمة المرور
6. أعد التشغيل!

## ⚙️ الإعداد

يقوم السكريبت بكتابة/تحديث الملفات التالية:

| الملف | الغرض |
| --- | --- |
| `/etc/dhcpcd.conf` | إضافة إعداد ثابت لنقطة الوصول على `wlan0` |
| `/etc/sysctl.d/routed-ap.conf` | ضبط `net.ipv4.ip_forward=1` |
| `/etc/dnsmasq.conf` | إعداد DHCP/DNS لنقطة الوصول (يُنقل الأصل إلى `/etc/dnsmasq.conf.orig`) |
| `/etc/hostapd/hostapd.conf` | إعدادات SSID والأمان والراديو |

### إعدادات hostapd الأساسية المستخدمة

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

لتخصيص إعدادات الراديو/الأمان الافتراضية بعد الإعداد، عدّل:

- `/etc/hostapd/hostapd.conf`

## 📝 ملاحظات

1. سيؤدي هذا إلى إنشاء شبكة WiFi بتردد 2.4GHz على القناة 2
2. تم ضبط رمز الدولة على الولايات المتحدة
3. لتغيير هذه الإعدادات، عدّل ملف `/etc/hostapd/hostapd.conf`
4. المجلد `i18n/` موجود في المستودع؛ ويمكن إضافة/تحديث ملفات README الخاصة باللغات المرتبطة فيه مع مرور الوقت.

## 🔍 أمثلة

وصّل جهازًا إلى SSID الذي قمت بإعداده، ثم تحقّق من التوجيه من Pi:

```bash
ip addr show wlan0
ip route
sudo iptables -t nat -S
systemctl status hostapd
```

تفاصيل الوصول المتوقعة على جانب نقطة الوصول بعد تنفيذ ناجح:

- عنوان IP لنقطة وصول Raspberry Pi: `10.20.1.1`
- اسم DNS المحلي: `rpi.ap`

## 🛠️ ملاحظات التطوير

- المنطق الرئيسي موجود في `setup.sh`
- لا يحتوي هذا المستودع حاليًا على مجموعة اختبارات أو خط أنابيب CI أو ملف manifest للحزم
- السكريبت عملي وبأسلوب imperative (يكتب مباشرة في إعدادات النظام)
- قد تؤدي إعادة التشغيل إلى إضافة أسطر مكررة في بعض الملفات (خصوصًا `/etc/dhcpcd.conf`)

## 🧰 استكشاف الأخطاء وإصلاحها

| المشكلة | التحقق / الإصلاح |
| --- | --- |
| `Passwords do not match` | أعد تشغيل `./setup.sh` وأدخل قيمًا متطابقة |
| نقطة الوصول غير ظاهرة بعد الإعداد | أعد تشغيل Pi، ثم تحقّق من `systemctl status hostapd` |
| لا يوجد إنترنت لأجهزة WiFi العميلة | تأكّد من أن وصلة الإيثرنت الصاعدة لديها إنترنت، وتحقّق من قاعدة NAT باستخدام `sudo iptables -t nat -S`، وتحقّق من التوجيه باستخدام `cat /etc/sysctl.d/routed-ap.conf` |
| مشاكل في بدء الخدمات | `sudo journalctl -u hostapd -b` و`sudo journalctl -u dnsmasq -b` |

## 🧭 خارطة الطريق

- إضافة منطق إعداد idempotent (إعادة تشغيل آمنة)
- إضافة سكريبت إلغاء تثبيت/إعادة ضبط لاستعادة الإعداد السابق
- إضافة دعم تجاوز الواجهات (للأنظمة التي لا تستخدم `wlan0`/`eth0`)
- إضافة فحوصات تحقق آلية وCI
- إضافة الترجمات المكتملة تحت `i18n/`

## 🤝 المساهمة

البلاغات (Issues) وطلبات السحب (Pull Requests) مرحّب بها.

عند المساهمة:

- أبقِ الأوامر والوثائق متوافقة مع سلوك السكريبت الفعلي
- اختبر على عتاد Raspberry Pi فعلي عند تغيير منطق الشبكات
- وثّق أي قيم افتراضية جديدة (القنوات، الشبكات الفرعية، الخدمات)

## 📄 الترخيص

هذا المشروع مرخّص تحت GNU General Public License v3.0.

راجع [LICENSE](../LICENSE).
