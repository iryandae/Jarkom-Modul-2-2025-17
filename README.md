# Komunikasi Data dan Jaringan Komputer — Modul 2

## Table of Contents

## 1. Pembuatan Topologi Mendasar

**Goal:** Pembuatan Topologi Mendasarmana Client melibatkan Eonwe (Router), l Client dan Elwing (West Subnet), Cirdan, Elrond, dan Maglor (East Subnet), dan Sirion, Tirion, Valmar, Lindon, dan Vingilot (DMZ Subnet).

### 1.1 Pengalamatan IP Address

| Name         | Location | IP Address  | Netmask         | Default Gateway | Description                |
| ------------ | -------- | ----------- | --------------- | --------------- | -------------------------- |
| **Eonwe**    | Router   | `10.72.1.1` | `255.255.255.0` | -               | West Subnet Gateway (eth1) |
|              |          | `10.72.2.1` | `255.255.255.0` | -               | East Subnet Gateway (eth2) |
|              |          | `10.72.3.1` | `255.255.255.0` | -               | DMZ Subnet Gateway (eth3)  |
| **Earendil** | West     | `10.72.1.2` | `255.255.255.0` | `10.72.1.1`     | West Client                |
| **Elwing**   | West     | `10.72.1.3` | `255.255.255.0` | `10.72.1.1`     | West Client                |
| **Cirdan**   | East     | `10.72.2.2` | `255.255.255.0` | `10.72.2.1`     | East Client                |
| **Elrond**   | East     | `10.72.2.3` | `255.255.255.0` | `10.72.2.1`     | East Client                |
| **Maglor**   | East     | `10.72.2.4` | `255.255.255.0` | `10.72.2.1`     | East Client                |
| **Sirion**   | DMZ      | `10.72.3.2` | `255.255.255.0` | `10.72.3.1`     | Reverse Proxy              |
| **Tirion**   | DMZ      | `10.72.3.3` | `255.255.255.0` | `10.72.3.1`     | DNS Master (ns1)           |
| **Valmar**   | DMZ      | `10.72.3.4` | `255.255.255.0` | `10.72.3.1`     | DNS Slave (ns2)            |
| **Lindon**   | DMZ      | `10.72.3.5` | `255.255.255.0` | `10.72.3.1`     | Static Web                 |
| **Vingilot** | DMZ      | `10.72.3.6` | `255.255.255.0` | `10.72.3.1`     | Dynamic Web                |

> [!NOTE]
> Netmasking done with `/16` CIDR Notation.
> Artinya menggunakan netmask `255.255.0.0` = `11111111.11111111.00000000.00000000`, dengan 10.72.xx.xx sebagai definisi IP Address lokal dalam konfigurasi ini.

### 1.2 Konfigurasi Tiap Network Node

**1. Eonwe**

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
         address 10.72.1.1
         netmask 255.255.255.0

auto eth2
iface eth2 inet static
         address 10.72.2.1
         netmask 255.255.255.0

auto eth3
iface eth3 inet static
         address 10.72..1
         netmask 255.255.255.0
```

**2. Earendil**

```
auto eth0
iface eth0 inet static
        address 10.72.1.2
        netmask 255.255.255.0
        gateway 10.72.1.1
```

**3. Elwing**

```
auto eth0
iface eth0 inet static
        address 10.72.1.3
        netmask 255.255.255.0
        gateway 10.72.1.1
```

**4. Cirdan**

```
auto eth0
iface eth0 inet static
        address 10.72.2.2
        netmask 255.255.255.0
        gateway 10.72.2.1
```

**5. Elrond**

```
auto eth0
iface eth0 inet static
        address 10.72.2.3
        netmask 255.255.255.0
        gateway 10.72.2.1
```

**6. Maglor**

```
auto eth0
iface eth0 inet static
        address 10.72.2.4
        netmask 255.255.255.0
        gateway 10.72.2.1
```

**7. Sirion**

```
auto eth0
iface eth0 inet static
        address 10.72.3.2
        netmask 255.255.255.0
        gateway 10.72.3.1
```

**8. Tirion**

```
auto eth0
iface eth0 inet static
        address 10.72.3.3
        netmask 255.255.255.0
        gateway 10.72.3.1
```

**9. Valmar**

```
auto eth0
iface eth0 inet static
        address 10.72.3.4
        netmask 255.255.255.0
        gateway 10.72.3.1
```

**10. Lindon**

```
auto eth0
iface eth0 inet static
        address 10.72.3.5
        netmask 255.255.255.0
        gateway 10.72.3.1
```

**11. Vingilot**

```
auto eth0
iface eth0 inet static
        address 10.72.3.6
        netmask 255.255.255.0
        gateway 10.72.3.1
```

## 2.
