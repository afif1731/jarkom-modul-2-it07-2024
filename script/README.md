## Client

- `client-script.sh`

```
echo nameserver 192.168.122.1 > /etc/resolv.conf

apt-get update
apt-get install lynx -y

echo nameserver 10.67.3.2 > /etc/resolv.conf
```

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

- `web_server_apache-script.sh`
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

- `web_server-script.sh`

```
echo nameserver 192.168.122.1 > /etc/resolv.conf

apt-get update
apt-get install lynx apache2 php libapache2-mod-php7.0 nginx -y

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

a2ensite jarkom-it07.conf
service apache2 restart
service apache2 stop
service nginx start
service nginx status

mkdir /var/www/jarkom-it07

echo '
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
' > /etc/nginx/sites-available/jarkom-it07

ln -s /etc/nginx/sites-available/jarkom-it07 /etc/nginx/sites-enabled
rm /etc/nginx/sites-enabled/default
service nginx restart
nginx -t
service php7.0-fpm start
```

- `web_server_nginx-script.sh`
```
echo nameserver 192.168.122.1 > /etc/resolv.conf

apt-get update
apt-get install nginx php php-fpm -y

echo nameserver 10.67.3.2 > /etc/resolv.conf

service apache2 stop

service nginx start

mkdir /var/www/jarkom-it07

cp /root/index.php /var/www/jarkom-it07/index.php

echo '
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
' > /etc/nginx/sites-available/jarkom-it07

rm /etc/nginx/sites-enabled/default

ln -s /etc/nginx/sites-available/jarkom-it07 /etc/nginx/sites-enabled

service php7.0-fpm start

service nginx restart
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

- `load_balancer_nginx-script.sh`
```
echo nameserver 192.168.122.1 > /etc/resolv.conf

apt-get update
apt-get install nginx php php-fpm -y

echo nameserver 10.67.3.2 > /etc/resolv.conf

service apache2 stop

service nginx start

echo "
upstream myita {
    server 10.67.1.3:8080; #stabler
    server 10.67.1.2:8080; #serverny
    server 10.67.1.4:8080; #lipvoka
}

server {
  listen 80;
  server_name 10.67.2.3;

  location / {
    proxy_pass http://myita;
  }
}
" > /etc/nginx/sites-available/jarkom-it07

rm /etc/nginx/sites-enabled/default

ln -s /etc/nginx/sites-available/jarkom-it07 /etc/nginx/sites-enabled

service nginx restart
```

## MyIta No. 16-18

- myita1618-script.sh
```
echo nameserver 192.168.122.1 > /etc/resolv.conf

apt-get update
apt-get install bind9 ufw -y

echo nameserver 10.67.3.2 > /etc/resolv.conf

mkdir /etc/bind/it07

echo '
zone "myIta.it07.com" {
    type master;
    file "/etc/bind/it07/myIta.it07.com";
};
' > /etc/bind/named.conf.local

echo '
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name _;

    return 301 http://www.myIta.it07.com$request_uri;
}

upstream myita {
    server 10.67.1.3:8080; #stabler
    server 10.67.1.2:8080; #serverny
    server 10.67.1.4:8080; #lipvoka
}

server {
  listen 80;
  server_name www.myIta.it07.com myIta.it07.com;

  location / {
    proxy_pass http://myita;
  }
}
' > /etc/nginx/sites-available/jarkom-it07

ln -s /etc/nginx/sites-available/jarkom-it07 /etc/nginx/sites-enabled

service nginx restart
service bind9 restart

ufw allow 14000
ufw allow 14400
ufw enable
```