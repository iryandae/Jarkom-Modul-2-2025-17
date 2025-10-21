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

## 11. Reverse Proxy
**Goal:** Menerapkan *path-based routing* dengan node **Sirion** berdiri sebagai reverse proxy, **Lindon** sebagai server static dengan *web content* yang dapat diakses melalui `/static`, dan **Vingilot** sebagai server web aplikasi yang dapat diakses melalui `/app`.

### 11.1 Konfigurasi Lindon & Vingilot
Pada node **Lindon**, kita perlu menambahkan `index.html` sebagai agar kita dapat membedakan ketika *reverse proxy* berusaha mengakses beberapa server yang berbeda.
```bash
echo "<h1>Halo dari Lindon (/static)</h1>" | tee /var/www/html/index.html
```
Setelah menambahkan file `index.html`, jangan lupa untuk melakukan *restart* pada layanan nginx.
```bash
service nginx restart
```
Lakukan hal yang serupa pada node **Vingilot** dengan perintah berikut:
```bash
echo "<h1>Halo dari Vingilot (/app)</h1>" | tee /var/www/html/index.html
```
### 11.2 Konfigurasi Sirion
Pada node **Sirion**, kita akan melakukan konfigurasi sebagai *reverse proxy* dengan mengubah file konfigurasi nginx milik **Sirion** atau membuat konfigurasi baru dengan menjalankan perintah berikut:
```bash
nano /etc/nginx/sites-available/reverse_proxy.conf
```
lalu, tulis ulang konfigurasi sebagai berikut:
```nginx
server {
    listen 80;
    server_name _;

    # Routing ke Lindon
    location /static/ {
        proxy_pass http://10.72.3.5/;
    }

    # Routing ke Vingilot
    location /app/ {
        proxy_pass http://10.72.3.6/;
    }
}
```
lalu, lakukan aktivasi terhadap konfigurasi tersebut dan *restart* layanan nginx pada node **Sirion**.
```bash
ln -s /etc/nginx/sites-available/reverse_proxy.conf /etc/nginx/sites-enabled/
nginx -t
service nginx restart
```
> note: Jika anda membuat file konfigurasi baru, harap menghapus konfigurasi default nginx untuk mencegah terjadinya *conflicts* ketika dijalankan: `rm /etc/nginx/sites-enabled/default`

### 11.3 Testing
Gunakan perintah `curl` dari node **Sirion** untuk melakukan test pada routing:
```bash
curl http://10.72.3.2/static/
curl http://10.72.3.2/app/
```
Masing-masing dari perintah tersebut, jika berhasil, seharusnya akan menampilkan *output* sesuai dengan apa yang sudah kita masukkan pada index.html masing-masing node.
```
<h1>Halo dari Lindon (/static)</h1>
<h1>Halo dari Vingilot (/app)</h1>
```

## 12. Secure Routing
**Goal:** Mencegah akses terlarang untuk *route* `/admin` dengan autentikasi dasar pada nginx.

### 12.1 Install Dependensi
Agar dapat memanfaatkan fitur autentikasi dasar menggunakan `htpasswd`, kita perlu dependensi menginstall `apache2-utils` pada node **Sirion**.
```bash
apt update
apt install nginx apache2-utils
```
### 12.2 Konfigurasi
Langkah pertama, kita perlu membuat file password yang akan menyimpan kredensial login untuk bagian `/admin`.
```bash
 htpasswd -c /etc/nginx/.htpasswd admin
```
Setelah menjalankan perintah tersebut, kita akan diminta untuk memasukkan kredensial password yang akan kita gunakan. Kemudian, kita juga perlu menambahkan konfigurasi untuk *path* `/admin` dengan konfigurasi keamanan. Tambahkan konfigurasi berikut pada file konfigurasi yang sama di soal sebelumnya:
```nginx
location /admin {
    auth_basic "Restricted Area";
    auth_basic_user_file /etc/nginx/.htpasswd;

    proxy_pass http://10.72.3.2/;
}
```
lalu, lakukan aktivasi terhadap konfigurasi tersebut dan *restart* layanan nginx pada node **Sirion**.
```bash
ln -s /etc/nginx/sites-available/reverse_proxy.conf /etc/nginx/sites-enabled/
nginx -t
service nginx restart
```

### 12.3 Testing
Ketika kita mencoba mengakses bagian `/admin` menggunakan perintah berikut:
```bash
curl http://10.72.3.2/admin/
```
Kita akan mendapatkan pesan `401 Authorization Required` yang menandakan perlu adanya *authorization* dengan proses autentikasi dengan kredensial yang sudah ditetapkan. Akses dengan kredensial dapat dilakukan dengan perintah berikut:
```bash
curl -u admin:sirion123 http://10.72.3.2/admin/
```
Seharusnya, jika berjalan dengan baik, perintah tersebut akan memberikan output 200 OK.

13. Redirect
**Goal:** Melakukan *redirect* untuk akses ke IP **Sirion** maupun **sirion.<xxxx>.com** harus melalui **www.<xxxx>.com** sebagai hostname kanonik.

### 13.1 Edit Konfigurasi
Langkah pertama, kita perlu mengubah konfigurasi pada file konfigurasi nginx yang kita pakai pada soal sebelumnya. Buka file nginx dan ubah konfigurasi menjadi seperti berikut:
```nginx
server {
    listen 80;
    server_name 10.72.3.2 sirion.K17.com;

    return 301 http://www.K17.com$request_uri;
}

# Main site configuration
server {
    listen 80;
    server_name www.K17.com;

    # Protected admin area
    location /admin {
        auth_basic "Restricted Area";
        auth_basic_user_file /etc/nginx/.htpasswd;

        proxy_pass http://10.72.3.2/;
    }

    # Routing ke Lindon
    location /static/ {
        proxy_pass http://10.72.3.5/;
    }

    # Routing ke Vingilot
    location /app/ {
        proxy_pass http://10.72.3.6/;
    }
}
```
Pada `/etc/hosts` tambahkan juga domain secara manual supaya node dapat mengenali domain yang dituju. Gunakan perintah berikut:
```bash
nano /etc/hosts
```
Tambahkan konfigurasi berikut pada file:
```
10.72.3.2   sirion.<xxxx>.com
10.72.3.2   www.<xxxx>.com
```
lalu, lakukan aktivasi terhadap konfigurasi tersebut dan *restart* layanan nginx pada node **Sirion**.
```bash
ln -s /etc/nginx/sites-available/reverse_proxy.conf /etc/nginx/sites-enabled/
nginx -t
service nginx restart
```

### 13.2 Testing
Ketika kita melakukan `curl` langsung ke alamat IP dengan perintah berikut:
```bash
curl -I http://10.72.3.2/
curl -I http://sirion.K17.com/
```
Jika berhasil, perintah akan memunculkan pesan dengan kode `301`. Sedangkan, jika kita mengakses dengan perintah berikut:
```bash
curl -I http://www.K17.com/
```
Perintah akan mengeluarkan pesan dengan kode `200` yang menandakan *routing* berhasil. Pastikan juga kita dapat mengakses bagian lain seperti:
```bash
curl -I http://www.K17.com/admin
curl -I http://www.K17.com/static
curl -I http://www.K17.com/app
```

## 14. Log IP
**Goal:** Memastikan access log aplikasi di **Vingilot** mencatat IP address klien asli saat lalu lintas melewati **Sirion**.

### 14.1 Konfigurasi
Pada node **Sirion**, tambahkan konfigurasi berikut untuk setiap routing yang bukan langsung ke Sirion (route ke IP **Lindon** dan **Vingilot**)
```nginx
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwaded_for;
proxy_set_header X-Forwarded-Proto $scheme;
```
Kemudian, pada node **Vingilot**, tambahkan konfigurasi berikut pada file `/etc/nignx/nginx.conf` untuk menangkap IP klienasli yang melintasi **Sirion**:
```nginx
http {
    # konfigurasi global lain...

    real_ip_header X-Forwarded-For;
    set_real_ip_from 10.72.3.2;
}
```
Setelah menyimpan file konfigurasi, lakukan verifikasi dan *restart* pada layanan nginx:
```bash
nginx -t
service nginx restart
```

### 14.2 Testing
Setelah melakukan konfigurasi, kita dapat melakukan tes dengan menjalankan perintah berikut pada node **Vingilot** untuk mendapatkan log terkait lalu lintas pada jaringan yang melewati **Sirion**:
```bash
tail -f /var/log/nginx/access.log
```
Lalu, ketika kita melakukan `curl` pada node client:
```bash
curl http://www.K17.com/app/
```
Pada terminal node **Vingilot** akan ditampilkan input log baru sebagai berikut:

<img width="935" height="309" alt="Cuplikan layar 2025-10-13 153515" src="https://github.com/user-attachments/assets/d9c37641-bb5c-4262-b90c-4dcfa07d6923" />

## 15. Uji Gelombang
**Goal:** Melakukan pengujian menggunakan **ApacheBench (ab)** dan merangkum hasil dalam tabel ringkas.

### 15.1
