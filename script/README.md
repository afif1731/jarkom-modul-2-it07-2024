## Pochinki

### Pastikan option.conf sudah ada di root

- `option.conf`
```

```

### Script

- `pochinki-script.sh`
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

### Pastikan option.conf sudah ada di root

- `option.conf`
```

```

### Script

- `georgopol-script.sh`
```
apt-get update
apt-get install dnsutils bind9 -y

mkdir /etc/bind/it07

echo '
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

zone "siren.redzone.it07.com" {
    type master;
    file "/etc/bind/it07/siren.redzone.it07.com";
    allow-transfer { 10.67.3.2; };
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

echo nameserver 10.67.3.2 > /etc/resolv.conf

service bind9 restart
```

## Web Server (Serverny, Lipovka, Stalber)

### Pastikan index.php sudah ada di root

- `index.php`
```
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

### Script

- `web_server-script.sh`
```
echo nameserver 192.168.122.1 > /etc/resolv.conf

apt-get update
apt-get install lynx apache2 php libapache2-mod-php7.0 nginx -y

echo nameserver 10.67.3.2 > /etc/resolv.conf

echo '
<VirtualHost *:8080>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/jarkom-it07
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
' > /etc/apache2/sites-available/jarkom-it07.conf

echo '
Listen 80
Listen 8080

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/ports.conf

mkdir /var/www/jarkom-it07

cp /root/index.php /var/www/jarkom-it07/index.php

a2enmod proxy
a2enmod proxy_http

cd /etc/apache2/sites-available

a2ensite jarkom-it07.conf

cd /root

service apache2 restart
```

## Load Balancer (MyIta)

- `load_balancer-script.sh`
```
echo nameserver 192.168.122.1 > /etc/resolv.conf

apt-get update
apt-get install lynx apache2 php libapache2-mod-php7.0 nginx -y

echo nameserver 10.67.3.2 > /etc/resolv.conf

echo '
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
' > /etc/apache2/sites-available/jarkom-it07.conf

echo '
Listen 80
Listen 8080

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/ports.conf

a2enmod proxy
a2enmod proxy_http
a2enmod proxy_balancer
a2enmod lbmethod_bytraffic

cd /etc/apache2/sites-available

a2ensite jarkom-it07.conf

cd /root

service apache2 restart
```