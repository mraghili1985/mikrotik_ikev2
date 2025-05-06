
# تنظیمات Mikrotik برای IKEv2و اتصال به یوزرمنیجر 

## 1. تنظیم ساعت سیستم (Clock)
برای تنظیم ساعت سیستم بر اساس زمان ایران:

```bash
/system clock
set time-zone-name=AsiaTehran
/system clock manual
set time-zone=+0330
```

---

## 2. تنظیم NTP (Network Time Protocol)
برای همگام‌سازی زمان با سرورهای NTP:

```bash
/system ntp client
set enabled=yes
system ntp client servers
add address=ir.pool.ntp.org
add address=0.asia.pool.ntp.org
add address=time.day.ir
```

---

## 3. فعال‌سازی گواهینامه SSL
برای فعال‌سازی SSL Certificate:

```bash
/certificate
enable-ssl-certificate dns-name=example.com
```

---

## 4. تنظیم Bridge
برای تنظیم IP Bridge به آدرس `10.0.0.1`:

```bash
/interface bridge
add name=bridge1 address=10.0.0.1
```

---

## 5. تعریف IP Pool
برای تعریف IP Pool از رنج `10.0.0.2` تا `10.0.0.254`:

```bash
/ip pool
add name=pool1 ranges=10.0.0.2-10.0.0.254
```

---

## 6. تنظیم NAT Exclude
برای خارج کردن IP `10.0.0.1` از NAT و انجام Masquerade برای سایر IP‌ها:

```bash
/ip firewall nat
# این قانون تمامی ترافیک‌هایی که آدرس مبدا آنها 10.0.0.1 نیست را از NAT عبور می‌دهد.
# برای سایر IPها، از Masquerade استفاده می‌شود که آدرس IP عمومی را به آنها اختصاص می‌دهد.
add chain=srcnat src-address=!10.0.0.1 action=masquerade
```

---

## 7. تنظیم Mode Config و DNS برای IP Pool
برای تنظیم DNS در Mode Config و IP Pool:

```bash
/ip ipsec mode-config
add name=mode-config1 address-pool=pool1 dns=8.8.8.8,8.8.4.4
```

---

## 8. تنظیم Proposal برای IKEv2
برای تنظیم Proposal با SHA256 و PFS:

```bash
/ip ipsec proposal
add name=proposal1 enc-algorithms=aes-256-cbc hash-algorithms=sha256 pfs-group=none
```

---

## 9. تنظیم Profile برای IKEv2
برای تنظیم Profile با AES و دیگر تنظیمات:

```bash
/ip ipsec profile
add name=profile1 dh-group=modp2048 enc-algorithms=aes-128-cbc,aes-192-cbc,aes-256-cbc
```

---

## 10. تنظیم Group برای IKEv2
برای تنظیم Group:

```bash
/ip ipsec group
add name=group1 group-type=ikev2
```

---

## 11. تنظیم Policy Template برای IKEv2
برای تنظیم Policy Template برای ارتباط‌های IKEv2:

```bash
/ip ipsec policy
add template=yes sa-src-address=10.0.0.1 sa-dst-address=10.0.0.2
```

---

## 12. تنظیم Peer IP برای IKEv2
برای تنظیم Peer IP به ایران:

```bash
/ip ipsec peer
add address=::/0 local-address=server IP peer-name=peer1 profile1 exhange Mode=IKEv2 passive=yes
```

---

## 13. تنظیم EAP (Extensible Authentication Protocol)
برای استفاده از EAP با Radius برای IKEv2:

```bash
/ppp profile
add name=eap-profile local-address=10.0.0.1 remote-address=pool1 use-radius=yes

/radius
add service=login,ipsec address=radius-server secret=secret123
```

---