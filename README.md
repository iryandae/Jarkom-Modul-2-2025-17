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
> [!NOTE]
> Jika anda membuat file konfigurasi baru, harap menghapus konfigurasi default nginx untuk mencegah terjadinya *conflicts* ketika dijalankan. Gunakan perintah berikut untuk menghapus file default: `rm /etc/nginx/sites-enabled/default`.

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
**Goal:** Melakukan *redirect* untuk akses ke IP **Sirion** maupun **sirion.K17.com** harus melalui **www.K17.com** sebagai hostname kanonik.

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

### 15.1 Konfigurasi
Kita perlu menginstall **ApacheBench** pada node **Elrond** untuk melakukan pengujian gelombang.
```bash
apt install apache2-utils
```
Setelah **ApacheBench** telah terinstall, kita perlu menambahkan konfigurasi DNS pada `/etc/hosts` menggunakan perintah `nano`. Tambahkan konfigurasi berikut pada file:
```
10.72.3.2         www.K17.com
```

### 15.2 Testing
Jika konfigurasi sudah dilakukan, selanjutnya kita dapat melakukan testing dengan perintah berikut untuk `/app`:
```bash
ab -n 500 -c 10 http://www.K17.com/app/
```
dan perintah berikut untuk `/static`:
```bash
ab -n 500 -c 10 http://www.K17.com/static/
```
Catat output yang didapatkan, atau juga dapat dilakukan perintah append ke dalam file untuk otomatis mencatat output dengan perintah berikut:
```bash
ab -n 500 -c 10 http://www.K17.com/app/ >> catatan.log
ab -n 500 -c 10 http://www.K17.com/static/ >> catatan.log
```
Jika output yang didapatkan kita ringkas ke dalam tabel dengan variabel yang diminta, maka akan mendapatkan hasil seperti tabel di bawah:

| Endpoint  | Req/sec  | Avg Time/Req (ms) | Transfer Rate (KB/s) |
|------------|----------|-------------------|----------------------|
| `/app/`    | **4670.02** | **2.141** | **1181.19** |
| `/static/` | **4312.32** | **2.319** | **1094.92** |

## 16. BIND
**Goal:** Mengubah *A Record* **Lindon**, menaikkan SOA serial pada **Tirion** dan memastikan **Valmar** ter-sinkron sebagai slave.

### 16.1 Konfigurasi
Pada node **Tirion**, ubah konfigurasi pada file *zone* `/etc/bind/k17/k17.com` menjadi sebagai berikut:
```
$TTL    604800
@       IN      SOA     ns1.k17.com. root.k17.com. (
                        2025100402 ; Serial
                        # ... baris SOA lainnya
                        )

# ... (record NS dan A lainnya) ...

lindon      30 IN       A      10.72.3.7   ;

# ... (record CNAME) ...
```
Di bagian SOA, naikan nomor serial dari konfigurasi sebelumnya. Tambahkan juga *A Record* untuk **Lindon** pada konfigurasi file seperti di atas. Setelah itu *restart* layanan `bind9` untuk menerapkan perubahan pada file *zone* tersebut:
```bash
service bind9 restart
```

### 16.2 Testing
Jalankan perintah `dig static.k17.com` pada node klien dan jika klien menerima IP baru **Lindon** (10.72.3.7), maka perubahan *A Record* di server DNS **Tirion** berhasil.

## 17. Service Auto-Start Configuration
**Goal:** Memastikan layanan inti pada setiap server (bind9 di ns1/ns2, nginx di **Sirion/Lindon**, dan PHP-FPM di **Vingilot**) dapat berjalan otomatis setelah reboot, untuk menjamin service resiliency tanpa intervensi manual.

### 17.1 Konfigurasi
Lakukan aktivasi layanan inti pada masing-masing node menggunakan perintah berikut pada node **Tirion** dan **Valmar**:
```bash
update-rc.d bind9 defaults
```

Pada node **Sirion**, aktifkan layanan **Nginx** dengan perintah berikut:
```bash
update-rc.d nginx defaults
```

Lalu, pada node **Lindon**, aktifkan layanan **Apache2** dengan perintah berikut:
```bash
update-rc.d apache2 defaults
```

Pada node **Vingilot**, aktifkan juga layanan **Apache2** dan **PHP-FPM** dengan perintah berikut:
```bash
update-rc.d apache2 defaults
update-rc.d php8.4-fpm defaults
```

### 17.2 Testing
Lakukan *reboot* untuk setiap node pada GNS3 menggunakan *interface* yang disediakan. Setelah sistem menyala kembali, pada node **Tirion**, jalankan perintah berikut:
```bash
service bind9 status
```
Jika didapat output `bind is running.` maka kita sudah berhasil melakukan konfigurasi *auto start*. Namun, jika output `bind is not running ... failed!` maka kemungkinan ada permasalahan pada *syntax* atau izin akses.

## 18. TXT dan CNAME Record
**Goal:** Menambahkan *record TXT* untuk `melkor.k17.com` berisi teks `"Morgoth (Melkor)"`, serta membuat record CNAME agar `morgoth.k17.com` menjadi alias yang menunjuk ke `melkor.k17.com.`. Kemudian, memverifikasi bahwa query TXT terhadap keduanya bekerja sesuai fungsi alias DNS.

### 18.1 Konfigurasi
Pada node **Tirion**, lakukan perubahan dengan menambahkan dua record baru ke *file zone* `/etc/bind/k17/k17.com` sebagai berikut:
```
melkor       IN       TXT      "Morgoth (Melkor)"
morgoth      IN       CNAME    melkor.k17.com.
```
Setelah menambahkan record, naikan nomor serial SOA pada *file zone* untuk menandai perubahan dan lakukan *restart* pada layanan `bind9`:
```bash
service bind9 restart
```

### 18.2 Testing
Pada node klien, lakukan validasi dengan menjalankan perintah `dig` untuk memastikan kedua record bekerja sesuai harapan:
```bash
dig melkor.k17.com TXT
```
Jika konfigurasi record TXT berhasil diterapkan, akan didapatkan output sebagai berikut:
```
melkor.k17.com.   3600   IN   TXT   "Morgoth (Melkor)"
```

Kemudian, jalankan juga perintah berikut untuk tes konfigurasi CNAME:
```bash
dig morgoth.k17.com TXT
```
Jika konfigurasi berhasil, akan didapatkan output dengan 2 baris seperti berikut:
```
morgoth.k17.com.  3600   IN   CNAME   melkor.k17.com.
melkor.k17.com.   3600   IN   TXT     "Morgoth (Melkor)"
```

## 19. CNAME Alias
**Goal:** Menambahkan record CNAME agar `havens.k17.com` menjadi alias dari `www.k17.com`, kemudian memastikan bahwa resolusi DNS dan alur rute aplikasi melalui reverse proxy berfungsi dengan benar dari dua klien berbeda.

### 19.1 Konfigurasi
Pada node **Tirion**, tambahkan konfigurasi tambahan pada *file zone* `/etc/bind/k17/k17.com`:
```
havens      IN       CNAME    www.k17.com.
```
Setelah itu, naikan nomor serial SOA untuk menandai perubahan dan lakukan *restart* pada `bind9` untuk menerapkan perubahan pada konfigurasi:
```bash
service bind9 restart
```

### 19.2 Testing
Pada dua node klien yang berbeda, coba akses hostname baru `heavens.k17.com` dengan perintah berikut:
```bash
curl http://havens.k17.com/app/
```
Jika konfigurasi berhasil, kedua perintah `curl` di atas akan menampilkan output:
```
<h1>Halo dari Vingilot (/app)</h1>
```

## 20. Landing Page
**Goal:** Menyediakan halaman depan berjudul **“War of Wrath: Lindon bertahan”** di server **Sirion**, yang berfungsi sebagai portal utama untuk mengakses dua layanan di belakang *reverse proxy*, yaitu `/app` dan `/static`.

### 20.1 Konfigurasi
Pada node **Sirion**, buat file `index.html` sebagai tampilan *landing page* dengan perintah berikut:
```bash
echo "<h1>War of Wrath: Lindon bertahan</h1>
<a href="http://www.k17.com/app">App</a>
<a href="http://www.k17.com/static">Static</a>" | tee /var/www/html/index.html
```
Karena Nginx pada Sirion telah dikonfigurasi sebelumnya untuk melayani file dari direktori `/var/www/html/` untuk path root `/`, tidak diperlukan perubahan konfigurasi tambahan pada Nginx. Namun, jika belum, bisa ditambahkan konfigurasi untuk path direktori yang diinginkan.

### 20.2 Testing
Pada node klien, lakukan akses ke *landing page* dengan perintah `curl` sebagai berikut:
```bash
curl http://www.k17.com/
```
Perintah akan memberikan output berupa isi file dari `index.html` yang ada pada node **Sirion** di dalam direktori `/var/www/html/` seperti berikut:
```
<h1>War of Wrath: Lindon bertahan</h1>
<a href="http://www.k17.com/app">App</a>
<a href="http://www.k17.com/static">Static</a>
```
