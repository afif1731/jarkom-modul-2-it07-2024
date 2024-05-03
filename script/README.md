## Pochinki

- pochinki-script.sh
```
apt-get update
apt-get install dnsutils bind9 -y

mkdir /etc/bind/it07

echo '
zone "airdrop.it07.com" {
        type master;
        notify yes;
        also-notify { 10.67.2.2; };
        allow-transfer { 10.67.2.2; };
        file "/etc/bind/it07/airdrop.it07.com";
};

zone "redzone.it07.com" {
        type master;
        notify yes;
        also-notify { 10.67.2.2; };
        allow-transfer { 10.67.2.2; };
        file "/etc/bind/it07/redzone.it07.com";
};

zone "loot.it07.com" {
        type master;
        notify yes;
        also-notify { 10.67.2.2; };
        allow-transfer { 10.67.2.2; };
        file "/etc/bind/it07/loot.it07.com";
};

zone "1.67.10.in-addr.arpa" {
        type master;
        notify yes;
        also-notify { 10.67.2.2; };
        allow-transfer { 10.67.2.2; };
        file "/etc/bind/it07/1.67.10.in-addr.arpa";
};
' > /etc/bind/named.conf.local

echo '
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
medkit        IN      A       10.67.1.4
www.medkit    IN      CNAME   medkit.airdrop.it07.com.

' > /etc/bind/it07/airdrop.it07.com

echo '
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
ns1           IN      A       10.67.2.2 ; IP Georgopol
siren         IN      NS      ns1

' > /etc/bind/it07/redzone.it07.com

echo '
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

' > /etc/bind/it07/loot.it07.com

echo '
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

' > /etc/bind/it07/1.67.10.in-addr.arpa

cp option.conf /etc/bind/named.conf.options

service bind9 restart
```

## Georgopol

- georgopol-script.sh
```
apt-get update
apt-get install dnsutils bind9 -y

mkdir /etc/bind/it07

echo '
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

zone "siren.redzone.it07.com" {
    type master;
    file "/etc/bind/it07/siren.redzone.it07.com";
    allow-transfer { 10.67.3.2; };
};
'

echo '
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

' > /etc/bind/it07/slave.airdrop.it07.com

echo '
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

' > /etc/bind/it07/slave.redzone.it07.com

echo '
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

' > /etc/bind/it07/slave.loot.it07.com

echo '
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
log         IN      A       10.67.1.2 ; IP Serverny
www.log     IN      CNAME   log.siren.redzone.it07.com.

' > /etc/bind/it07/siren.redzone.it07.com

service bind9 restart
```