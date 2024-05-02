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