# Laporan Resmi Praktikum Jarkom Modul 2 - Kelompok IT07

| Nama              | NRP        |
| ----------------- | ---------- |
| Muhammad Afif     | 5027221032 |
| Alma Amira Dewani | 5027221054 |

Praktikum modul 2 jarkom terdiri dari 20 soal yang dikerjakan seluruhnya menggunakan `VM GNS3`. Ketentuan tambahannya adalah node ubuntu untuk praktikum ini harus menggunakan docker image `kuuhaku86/gns3-ubuntu:1.0.1`.

Berikut merupakan cara penyelesaian modul oleh kelompok IT07. Penyelesaian dilakukan hingga nomor 18, nomor 19 dan 20 ditambahkan pada bagian revisi karena dalam pengerjaan kelompok IT07 hanya mencapai nomor 18 dan nomor sisanya diselesaikan di luar waktu praktikum.

## No.1

Untuk membantu pertempuran di Erangel, kamu ditugaskan untuk membuat jaringan komputer yang akan digunakan sebagai alat komunikasi. Sesuaikan rancangan Topologi dengan rancangan dan pembagian yang berada di link yang telah disediakan

### Topologi :

![topologi](./pictures/02.png)

Dari topologi yang telah dibuat, dilakukan konfigurasi pada setiap node seperti berikut

### GNS3 Network Configuration

- Erangel : Router

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.67.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.67.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.67.3.1
	netmask 255.255.255.0
```

- Pochinki : DNS Master

```
auto eth0
iface eth0 inet static
	address 10.67.3.2
	netmask 255.255.255.0
	gateway 10.67.3.1
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- Georgopol : DNS Slave

```
auto eth0
iface eth0 inet static
	address 10.67.2.2
	netmask 255.255.255.0
	gateway 10.67.2.1
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- Ruins : Client

```
auto eth0
iface eth0 inet static
	address 10.67.2.4
	netmask 255.255.255.0
	gateway 10.67.2.1
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- Apartments : Client

```
auto eth0
iface eth0 inet static
	address 10.67.2.5
	netmask 255.255.255.0
	gateway 10.67.2.1
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- MyIta : Load Balancer

```
auto eth0
iface eth0 inet static
	address 10.67.2.3
	netmask 255.255.255.0
	gateway 10.67.2.1
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- Serverny : Web Server

```
auto eth0
iface eth0 inet static
	address 10.67.1.2
	netmask 255.255.255.0
	gateway 10.67.1.1
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- Stalber : Web Server

```
auto eth0
iface eth0 inet static
	address 10.67.1.3
	netmask 255.255.255.0
	gateway 10.67.1.1
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

- Lipovka : Web Server

```
auto eth0
iface eth0 inet static
	address 10.67.1.4
	netmask 255.255.255.0
	gateway 10.67.1.1
        up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Pengaturan tambahan pada Erangel

Agar dapat tersambung pada internet, ditambahkan command berikut pada file `.bashrc` yang ada pada node Erangel

- `/root/.bashrc`

```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.67.0.0/16
```

## No.2

Karena para pasukan membutuhkan koordinasi untuk mengambil airdrop, maka buatlah sebuah domain yang mengarah ke Stalber dengan alamat airdrop.xxxx.com dengan alias www.airdrop.xxxx.com dimana xxxx merupakan kode kelompok. Contoh : airdrop.it01.com

### Install dependensi di Pochinki

Sebelum pengerjaan dilakukan, diperlukan penginstallan `bind9` pada DNS master

- terminal

```
apt-get update
apt-get install dnsutils bind9 -y
```

### Konfigurasi named.conf.local

Inisiasi untuk `airdrop.it07.com` dimasukkan pada file bind

- /etc/bind/named.conf.local

```
zone "airdrop.it07.com" {
        type master;
        file "/etc/bind/it07/airdrop.it07.com";
};
```

### Konfigurasi Zone

buat direktori `it07` pada direktori bind melalui `mkdir /etc/bind/it07` lalu buat file `airdrop.it07.com` yang akan diisi oleh konfigurasi zone airdrop.

- /etc/bind/it07/airdrop.it07.com

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     airdrop.it07.com. root.airdrop.it07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@             IN      NS      airdrop.it07.com.
@             IN      A       10.67.1.3 ; IP Stalber
www           IN      CNAME   airdrop.it07.com.
```

Restart service bind9 agar konfigurasi zone yang dilakukan dapat berjalan

```
service bind9 restart
```

### Untuk pengujian, masuk ke salah satu terminal node client

Agar DNS yang telah dikonfigurasi dapat dijalankan, ubah nameserver pada node client menjadi `IP dari Pochinki`

- Ubah nameserver client Ruins dan Apartments ke Pochinki

```
#nameserver 192.168.122.1
nameserver 10.67.3.2
```

- lakukan testing di Ruins dan Apartments

```
ping airdrop.it07.com
```

- Hasilnya adalah command ping berhasil mengirim request pada `IP Stalber`

![ping ke airdrop](./pictures/no2_1.png)

## No.3

Para pasukan juga perlu mengetahui mana titik yang sedang di bombardir artileri, sehingga dibutuhkan domain lain yaitu redzone.xxxx.com dengan alias www.redzone.xxxx.com yang mengarah ke Severny

### Konfigurasi DNS

lakukan perubahan pada file `named.conf.local` untuk melakukan inisiasi zone redzone. Tambahkan teks berikut pada file

- /etc/bind/named.conf.local

```
zone "redzone.it07.com" {
        type master;
        file "/etc/bind/it07/redzone.it07.com";
};
```

Buat file `redzone.it07.com` pada direktori it07 yang telah dibuat pada no. 2 lalu masukkan teks berikut pada file

- /etc/bind/it07/redzone.it07.com

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it07.com. root.redzone.it07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@             IN      NS      redzone.it07.com.
@             IN      A       10.67.1.2 ; IP redzone
www           IN      CNAME   redzone.it07.com.
```

### Untuk pengujian, masuk ke salah satu terminal node client

lakukan ping pada `redzone.it07.com` dan `www.redzone.it07.com`. Hasilnya adalah seperti berikut

![ping redzone](./pictures/no3_1.png)

## No.4

Markas pusat meminta dibuatnya domain khusus untuk menaruh informasi persenjataan dan suplai yang tersebar. Informasi persenjataan dan suplai tersebut mengarah ke Mylta dan domain yang ingin digunakan adalah loot.xxxx.com dengan alias www.loot.xxxx.com

### Konfigurasi DNS

lakukan perubahan pada file `named.conf.local` untuk melakukan inisiasi zone loot. Tambahkan teks berikut pada file

- /etc/bind/named.conf.local

```
zone "loot.it07.com" {
        type master;
        file "/etc/bind/it07/loot.it07.com";
};
```

Buat file `loot.it07.com` pada direktori it07 yang telah dibuat pada no. 2 lalu masukkan teks berikut pada file

- /etc/bind/it07/loot.it07.com

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     loot.it07.com. root.loot.it07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@             IN      NS      loot.it07.com.
@             IN      A       10.67.2.3 ; IP loot
www           IN      CNAME   loot.it07.com.
```

### Untuk pengujian, masuk ke salah satu terminal node client

lakukan ping pada `loot.it07.com` dan `www.loot.it07.com`. Hasilnya adalah seperti berikut

![ping loot](./pictures/no4_1.png)

## No. 5

Pastikan domain-domain tersebut dapat diakses oleh seluruh komputer (client) yang berada di Erangel

### Ujicoba pada Ruins dan Apartments

Ketika melakukan pengujian pada client, pastikan nameserver sudah mengarah pada DNS Master (Pochinki)

- /etc/resolv.conf

```
#nameserver 192.168.122.1
nameserver 10.67.3.2
```

- sebelumnya telah dilakukan pengujian pada `Ruins`, sekarang juda dilakukan pengujian pada `Apartments`

![ping di apartments](./pictures/no5_1.png)

## No. 6

Beberapa daerah memiliki keterbatasan yang menyebabkan hanya dapat mengakses domain secara langsung melalui alamat IP domain tersebut. Karena daerah tersebut tidak diketahui secara spesifik, pastikan semua komputer (client) dapat mengakses domain redzone.xxxx.com melalui alamat IP Severny (Notes : menggunakan pointer record)

### Konfigurasi Reverse DNS

lakukan perubahan pada file `named.conf.local` untuk melakukan inisiasi zone reverse ip Serverny. Tambahkan teks berikut pada file

- /etc/bind/named.conf.local

```
zone "1.67.10.in-addr.arpa" {
        type master;
        file "/etc/bind/it07/1.67.10.in-addr.arpa";
};
```

Buat file `1.67.10.in-addr.arpa` pada direktori it07 yang telah dibuat pada no. 2 lalu masukkan teks berikut pada file

- /etc/bind/it07/1.67.10.in-addr.arpa

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it07.com. root.redzone.it07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
; PTR Records
1.67.10.in-addr.arpa.    IN      NS      redzone.it07.com.
2                        IN      PTR     redzone.it07.com.
```

### Pengujian pada Ruins dan Apartments

jalankan perintah berikut untuk melihat arah pointer dari IP Serverny

- terminal client

```
host -t PTR 10.67.1.2
```

Apabila berhasil, maka tampilannya adalah seperti berikut

![host PTR](./pictures/no6_1.png)

## No.7

Akhir-akhir ini seringkali terjadi serangan siber ke DNS Server Utama, sebagai tindakan antisipasi kamu diperintahkan untuk membuat DNS Slave di Georgopol untuk semua domain yang sudah dibuat sebelumnya

### Menambahkan konfigurasi pada Pochinki

Tambahkan baris berikut pada semua zone

- /etc/bind/named.conf.local

```
notify yes;
also-notify { 10.67.2.2; };
allow-transfer { 10.67.2.2; };
```

- Contoh pada salah satu zone

```
zone "1.67.10.in-addr.arpa" {
        type master;
        notify yes;
        also-notify { 10.67.2.2; };
        allow-transfer { 10.67.2.2; };
        file "/etc/bind/it07/1.67.10.in-addr.arpa";
};
```

### Konfigurasi pada di Georgopol

Install `bind9` pada node Georgopol lalu ubah isi file /etc/bind/named.conf.local untuk melakukan inisiasi DNS Slave

- /etc/bind/named.conf.local

```
zone "airdrop.it07.com" {
    type slave;
    masters { 10.67.3.2; };
    file "/etc/bind/it07/slave.airdrop.it07.com";
};

zone "redzone.it07.com" {
    type slave;
    masters { 10.67.3.2; };
    file "/etc/bind/it07/slave.redzone.it07.com";
};

zone "loot.it07.com" {
    type slave;
    masters { 10.67.3.2; };
    file "/etc/bind/it07/slave.loot.it07.com";
};

zone "1.67.10.in-addr.arpa" {
        type master;
        notify yes;
        also-notify { 10.67.2.2; };
        allow-transfer { 10.67.2.2; };
        file "/etc/bind/it07/1.67.10.in-addr.arpa";
};
```

kemudian buat direktori it07 pada direktori /etc/bind yang isinya sama seperti pada DNS master

- /etc/bind/it07/slave.airdrop.it07.com

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     airdrop.it07.com. root.airdrop.it07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@             IN      NS      airdrop.it07.com.
@             IN      A       10.67.1.3 ; IP airdrop
www           IN      CNAME   airdrop.it07.com.
```

- /etc/bind/it07/slave.redzone.it07.com

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it07.com. root.redzone.it07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@             IN      NS      redzone.it07.com.
@             IN      A       10.67.1.2 ; IP redzone
www           IN      CNAME   redzone.it07.com.
```

- /etc/bind/it07/slave.loot.it07.com

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     loot.it07.com. root.loot.it07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@             IN      NS      loot.it07.com.
@             IN      A       10.67.2.3 ; IP loot
www           IN      CNAME   loot.it07.com.
```

- /etc/bind/it07/1.67.10.in-addr.arpa

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it07.com. root.redzone.it07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
; PTR Records
1.67.10.in-addr.arpa.    IN      NS      redzone.it07.com.
2                        IN      PTR     redzone.it07.com.
```

### Pengujian pada client Ruins dan Apartments

Ubah nameserver pada cilent menjadi ip dari Georgopol untuk mensimulasikan bahwa Pochinki sedang down

```
#nameserver 192.168.122.1
#nameserver 10.67.3.2
nameserver 10.67.2.2
```

lalu jalankan ping pada domain yang sama, hasilnya adalah seperti berikut

![ping 7](./pictures/no7_1.png)

## No.8

Kamu juga diperintahkan untuk membuat subdomain khusus melacak airdrop berisi peralatan medis dengan subdomain medkit.airdrop.xxxx.com yang mengarah ke Lipovka

### Tambahan konfigurasi pada /etc/bind/it07/airdrop.it07.com

Tambahkan konfigurasi untuk sobdomain medkit pada `airdrop.it07.com`

- /etc/bind/it07/airdrop.it07.com

```
medkit        IN      A       10.67.1.4
www.medkit    IN      CNAME   medkit.airdrop.it07.com.
```

### Pengujian pada client

Jalankan `ping medkit.airdrop.it07.com` dan `ping www.medkit.airdrop.it07.com`, hasilnya adalah sebagai berikut

![ping medkit](./pictures/no8_1.png)

## No.9

Terkadang red zone yang pada umumnya di bombardir artileri akan dijatuhi bom oleh pesawat tempur. Untuk melindungi warga, kita diperlukan untuk membuat sistem peringatan air raid dan memasukkannya ke subdomain siren.redzone.xxxx.com dalam folder siren dan pastikan dapat diakses secara mudah dengan menambahkan alias www.siren.redzone.xxxx.com dan mendelegasikan subdomain tersebut ke Georgopol dengan alamat IP menuju radar di Severny

#### Tambahan konfigurasi pada /etc/bind/it07/redzone.it07.com

Tambahkan konfigurasi untuk delegasi subdomain pada Pochinki yang mengarah ke Georgopol

- /etc/bind/it07/redzone.it07.com

```
ns1           IN      A       10.67.2.2 ; IP Georgopol
siren         IN      NS      ns1
```

Agar delegasi dapat memngirimkan query yang terpasang pada domain, maka perlu ditambahkan opsi allow-query

- /etc/bind/named.conf.options

```
allow-query{any;};
```

Pastikan juga forwarders pada `named.conf.options` tidak aktif

```
//forwarders {
//    0.0.0.0;
//};
```

#### Konfigurasi pada Georgopol

Tambahkan zone siren.redzone yang menerima transfer dari Pochinki

- /etc/bind/named.conf.local

```
zone "siren.redzone.it07.com" {
    type master;
    file "/etc/bind/it07/siren.redzone.it07.com";
    allow-transfer { 10.67.3.2; };
};
```

Buat juga file `siren.redzone.it07.com`

- /etc/bind/it07/siren.redzone.it07.com

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     siren.redzone.it07.com. root.siren.redzone.it07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      siren.redzone.it07.com.
; DNS Records
@           IN      A       10.67.1.2
www         IN      CNAME   siren.redzone.it07.com.
```

### Pengujian pada client

![ping siren](./pictures/no9_1.png)

## No. 10

### soal

Markas juga meminta catatan kapan saja pesawat tempur tersebut menjatuhkan bom, maka buatlah subdomain baru di subdomain siren yaitu log.siren.redzone.xxxx.com serta aliasnya www.log.siren.redzone.xxxx.com yang juga mengarah ke Severny

- tambahkan ini di georgopol /etc/bind/it07/siren.redzone.it07.com

```
log         IN      A       10.67.1.2 ; IP Serverny
www.log     IN      CNAME   log.siren.redzone.it07.com.
```

### Output

![nomor10](./img/no.10-1.png)

![nomor10.1](./img/no.10-2.png)

## No.11

### soal

Setelah pertempuran mereda, warga Erangel dapat kembali mengakses jaringan luar, tetapi hanya warga Pochinki saja yang dapat mengakses jaringan luar secara langsung. Buatlah konfigurasi agar warga Erangel yang berada diluar Pochinki dapat mengakses jaringan luar melalui DNS Server Pochinki

- comment bagian ini di Pochinki /etc/bind/named.conf.options

```
// dnssec-validation auto;
```

- dan uncomment + atur jadi begini

```
forwarders {
    192.168.122.1;
};
```

### Output

![nomor11](./img/no.11.png)

## No.12

### Soal

Karena pusat ingin sebuah website yang ingin digunakan untuk memantau kondisi markas lainnya maka deploy lah webiste ini (cek resource yg lb) pada severny menggunakan apache

- instalasi kebutuhan

```
apt-get update
apt-get install lynx apache2 php libapache2-mod-php7.0 nginx -y
```

- masuk ke /etc/apache2/sites-available lalu copy file defaultnya

```
cp 000-default.conf jarkom-it07.conf
```

- ubah isi filenya jarkom-it07.conf

Hanya ubah bagian ini saja

```
<VirtualHost *:8080> #ubah portnya menjadi 8080

ServerAdmin webmaster@localhost
DocumentRoot /var/www/jarkom-it07 #ubah document rootnya
```

- Tambahkan port 8080 di /etc/apache2/ports,conf

```
Listen 8080
```

- Aktifkan konfigurasi

```
a2ensite jarkom-it07.conf
```

- Restart apache

```
service apache2 restart
```

- buat directory jarkom-it07 di /var/www lalu tambahkan index phpnya

```
mkdir jarkom-it07


<?php
$hostname = gethostname();
$date = date('Y-m-d H:i:s');
$php_version = phpversion();
$username = get_current_user();



echo "Hello World!<br>";
echo "Saya adalah: $username<br>";
echo "Saat ini berada di: $hostname<br>";
echo "Versi PHP yang saya gunakan: $php_version<br>";
echo "Tanggal saat ini: $date<br>";
?>
```

- jalankan

```
lynx http://10.67.1.2:8080
```

### Output

![nomor12](./img/no.12.png)

## No.13

Tapi pusat merasa tidak puas dengan performanya karena traffic yag tinggi maka pusat meminta kita memasang load balancer pada web nya, dengan Severny, Stalber, Lipovka sebagai worker dan Mylta sebagai Load Balancer menggunakan apache sebagai web server nya dan load balancernya

### Web-server

- Jalankan Keseluruhan No.12 di semua web-server (karena Serverny sudah, maka tinggal dikerjakan di Stalber dan Lipovka)

- Kemudian Jalankan Command - command ini di setaip web-server

```
a2enmod proxy
a2enmod proxy_http
service apache2 restart
```

### Load balancer

- Install dependensi

```
apt-get update
apt-get install lynx apache2 php libapache2-mod-php7.0 nginx -y
```

- buat file /etc/apache2/sites-available/jarkom-it07.conf

- lalu masukkan

```
<VirtualHost *:8080>
        <proxy balancer://itbalancer>
                BalancerMember http://10.67.1.2:8080
                BalancerMember http://10.67.1.3:8080
                BalancerMember http://10.67.1.4:8080
                ProxySet lbmethod=bytraffic
        </proxy>
        ProxyPreserveHost On
        ProxyPass / balancer://itbalancer/
        ProxyPassReverse / balancer://itbalancer/
</VirtualHost>
```

ProxySet bytraffice berarti load-balancer menggunakan metode round robin

- atur port tambahan. Tambahkan line berikut di /etc/apache2/ports.conf

```
Listen 8080
```

- Jalankan semua command berikut

```
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_balancer
a2enmod lbmethod_bytraffic
```

- pindah direktori ke /etc/apache2/sites-available

- lalu jalankan

```
a2ensite jarkom-it07.conf
```

- kembali ke root dan restart

```
service apache2 restart
```

### Output

![nomor13.2](./img/no13-2.png)

![nomor13.4](./img/no13-4.png)

![nomor13.1](./img/no13-1.png)

![nomor13.5](./img/no.13-5.png)

## No.14

Mereka juga belum merasa puas jadi pusat meminta agar web servernya dan load balancer nya diubah menjadi nginx

### Load Balancer

- Instalasi dependensi

```
apt-get update
apt-get install lynx apache2 php libapache2-mod-php7.0 nginx -y php -y php-fpm -y
```

- Jalankan nginx

```
service nginx start
```

- Masukan konfigurasi load balancer ke /etc/nginx/sites-available/jarkom-it07

```
upstream myita {
    server 10.67.1.3:8001; #stabler
    server 10.67.1.2:8002; #serverny
    server 10.67.1.4:8003; #lipvoka
}

server {
  listen 80;
  server_name 10.67.2.3;

  location / {
    proxy_pass http://myita;
  }
}
```

- Menjalankan symlink

```
ln -s /etc/nginx/sites-available/jarkom-it07 /etc/nginx/sites-enabled
```

- Menghapus default pada nginx agar tidak terjadi konflik

```
rm /etc/nginx/sites-enabled/default
```

- Menjalankan nginx

```
service nginx restart
```

### Web Worker

- Pastikan sudah menginstall dependensi
- Pastikan sudah mensetting port 8080
- Pastikan juga sudah menambahkan index.php di /var/www/jarkom-it07

- Jalankan nginx

```
service nginx start
```

- Buat konfigurasi di /etc/nginx/sites-available/jarkom-it07

```
server {

    listen 8080;

    root /var/www/jarkom-it07;

    index index.php index.html index.htm;
    server_name _;

    location / {
        try_files \$uri \$uri/ /index.php?\$query_string;
    }

    # pass PHP scripts to FastCGI server
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
    }

    location ~ /\.ht {
     deny all;
    }

    error_log /var/log/nginx/jarkom-it07_error.log;
    access_log /var/log/nginx/jarkom-it07_access.log;
}
```

- Menajalankan symlink

```
ln -s /etc/nginx/sites-available/jarkom-it07 /etc/nginx/sites-enabled
```

- Menghapus default pada nginx agar tidak terjadi konflik

```
rm /etc/nginx/sites-enabled/default
```

- Menjalankan nginx

```
service nginx restart
```

```
service php7.0-fpm start
```

### Output

![nomor14.1](./img/no.14-1.png)

![nomor14.2](./img/no.14-2.png)

![nomor14.3](./img/no.14-3.png)

![nomor14.4](./img/no.14-4.png)

![nomor14.5](./img/no.14-5.png)

![nomor14.6](./img/no.14-6.png)

## No. 15

### Client

- pastikan install apache benchmark

```
apt-get install apache2-utils
```

- Jalankan perintah ini

```
ab -n 200 -c 10 http://10.67.2.3/
```

- `n`: jumlah request yang akan dikirim
- `c`: concurrency, jumlah request dalam satu pengiriman

```
This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 10.67.2.3 (be patient)
Completed 100 requests
Completed 200 requests
Finished 200 requests


Server Software:        nginx/1.10.3
Server Hostname:        10.67.2.3
Server Port:            80

Document Path:          /
Document Length:        167 bytes

Concurrency Level:      10
Time taken for tests:   0.189 seconds
Complete requests:      200
Failed requests:        133
   (Connect: 0, Receive: 0, Length: 133, Exceptions: 0)
Total transferred:      62467 bytes
HTML transferred:       33267 bytes
Requests per second:    1056.36 [#/sec] (mean)
Time per request:       9.466 [ms] (mean)
Time per request:       0.947 [ms] (mean, across all concurrent requests)
Transfer rate:          322.20 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        1    2   0.8      2       6
Processing:     5    7   1.4      7      12
Waiting:        5    7   1.4      7      12
Total:          7    9   1.5      9      15

Percentage of the requests served within a certain time (ms)
  50%      9
  66%      9
  75%     10
  80%     10
  90%     12
  95%     14
  98%     14
  99%     14
 100%     15 (longest request)
```

- Simpan hasilnya
- Ubah Load balancer pada `myIta`, lalu jalankan command yang sama lagi

- Round-Robin

```
# nda perlu tambahkan apa-apa
```

- Least-connection

```
least_conn;
```

- IP Hash

```
ip_hash;
```

- Generic Hash

```
hash $request_uri consistent;
```

## No. 16 dan 18

### di pochinki

- tambahkan ini pada /etc/bind/named.conf.local

```
one "myIta.it07.com" {
    type master;
    file "/etc/bind/it07/myIta.it07.com";
};

zone "2.67.10.in-addr.arpa" {
        type master;
        notify yes;
        also-notify { 10.67.2.2; };
        allow-transfer { 10.67.2.2; };
        file "/etc/bind/it07/2.67.10.in-addr.arpa";
};
```

- ini pada /etc/bind/it07/myIta.it07.com

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     myIta.it07.com. root.myIta.it07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@             IN      NS      myIta.it07.com.
@             IN      A       10.67.2.3 ; IP myIta
www           IN      CNAME   myIta.it07.com.
```

- ini pada /etc/bind/it07/2.67.10.in-addr.arpa

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     myIta.it07.com. root.myIta.it07.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
; PTR Records
2.67.10.in-addr.arpa.    IN      NS      myIta.it07.com.
3                        IN      PTR     www.myIta.it07.com.
```

## No. 17

- masukkan pada nginx myIta

```
upstream myita {
    server 10.67.1.3:8080; #stabler
    server 10.67.1.2:8080; #serverny
    server 10.67.1.4:8080; #lipvoka
}

server {
  listen 14000;
  listen 14400;
  server_name 10.67.2.3;

  location / {
    proxy_pass http://myita;
  }
}
" > /etc/nginx/sites-available/jarkom-it07
```

# Revisi

## No.15

Sebelumnya, IT07 mengalami salah pemahaman pada soal yang menyebabkan pengujian website menggunakan apache benchmark hanya dilakukan pada nginx. Pada revisi ini, laporan pengujian telah ditambahkan pada web server apache2. Laporan dapat diakses melalui [link ini](https://docs.google.com/document/d/1rsLhPZcboGSeD-4F5tlF4qKTCWgRor3E-LaF7eXnQk0/edit?usp=sharing)

## No. 19

Berikut merupakan penyelesaian Soal Nomor 19 yang sebelumnya tidak sempat dikerjakan

## No. 20

Berikut merupakan penyelesaian Soal Nomor 20 yang sebelumnya tidak sempat dikerjakan
