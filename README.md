# Keperluan Copyable

## No.1

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

### Tambahkan ini di Erangel
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.67.0.0/16
```

## No.2

### Script

- /root/script.sh
```
apt-get update
apt-get install dnsutils bind9 -y
```

### Config
- /etc/bind/named.conf.local
```
zone "airdrop.it07.com" {
        type master;
        file "/etc/bind/it07/airdrop.it07.com";
};
```

### Sebelum config DNS, selalu lakukan
```
cp /etc/bind/db.local /etc/bind/it07/name.it07.com
```

### Setelah config, restart pakai
```
service bind9 restart
```

### Config DNS

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
@             IN      A       10.67.1.3 ; IP airdrop
www           IN      CNAME   airdrop.it07.com.
```

- Ubah nameserver client Ruins ke Pochinki
```
#nameserver 192.168.122.1
nameserver 10.67.3.2
```

- testing di Ruins
```
ping airdrop.it07.com
```

- Ubah nameserver client Apartments ke Pochinki
```
#nameserver 192.168.122.1
nameserver 10.67.3.2
```

- testing di Apartments
```
ping airdrop.it07.com
```

## No.3

Mirip No. 2 cuma beda nama

### Config DNS

- /etc/bind/named.conf.local
```
zone "redzone.it07.com" {
        type master;
        file "/etc/bind/it07/redzone.it07.com";
};
```

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

## No.4

Mirip No. 2 cuma beda nama

### Config DNS

- /etc/bind/named.conf.local
```
zone "loot.it07.com" {
        type master;
        file "/etc/bind/it07/loot.it07.com";
};
```

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

## No. 5

Tinggal tes dari Apartments sama Ruins

## No. 6

### Config Reverse DNS

- /etc/bind/named.conf.local
```
zone "1.67.10.in-addr.arpa" {
        type master;
        file "/etc/bind/it07/1.67.10.in-addr.arpa";
};
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

### Periksa melalui
```
host -t PTR 10.67.1.2
```

## No.7

- Tambahkan di semua zone /etc/bind/named.conf.local
```
notify yes;
        also-notify { 10.67.2.2; };
        allow-transfer { 10.67.2.2; };
```

Invert DNS nda usah

### Config di Georgopol

- /etc/bind/named.conf.local
```
zone "airdrop.it07.com" {
    type slave;
    masters { 10.67.3.2; }; # IP Yudhistira
    file "/etc/bind/it07/slave.airdrop.it07.com";
};

zone "redzone.it07.com" {
    type slave;
    masters { 10.67.3.2; }; # IP Yudhistira
    file "/etc/bind/it07/slave.redzone.it07.com";
};

zone "loot.it07.com" {
    type slave;
    masters { 10.67.3.2; }; # IP Yudhistira
    file "/etc/bind/it07/slave.loot.it07.com";
};
```

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

## No.8

- tambahkan di /etc/bind/it07/airdrop.it07.com
```
medkit        IN      A       10.67.1.4
www.medkit    IN      CNAME   medkit.airdrop.it07.com.
```

## No.9

#### Di Pochinki

- tambahkan ini di /etc/bind/it07/redzone.it07.com
```
ns1           IN      A       10.67.2.2 ; IP Georgopol
siren         IN      NS      ns1
```

- tambahkan ini di /etc/bind/named.conf.options
```
allow-query{any;};
```


#### Di Georgopol

- /etc/bind/named.conf.local
```
zone "siren.redzone.it07.com" {
    type master;
    file "/etc/bind/it07/siren.redzone.it07.com";
    allow-transfer { 10.67.3.2; };
};
```

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

## No. 10

- tambahkan ini di georgopol /etc/bind/it07/siren.redzone.it07.com
```
log         IN      A       10.67.1.2 ; IP Serverny
www.log     IN      CNAME   log.siren.redzone.it07.com.
```

## No.11

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