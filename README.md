# Keperluan Copyable

## GNS3 Network Configuration

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
```

- Georgopol : DNS Slave
```
auto eth0
iface eth0 inet static
	address 10.67.2.2
	netmask 255.255.255.0
	gateway 10.67.2.1
```

- Ruins : Client
```
auto eth0
iface eth0 inet static
	address 10.67.2.4
	netmask 255.255.255.0
	gateway 10.67.2.1
```

- Apartments : Client
```
auto eth0
iface eth0 inet static
	address 10.67.2.5
	netmask 255.255.255.0
	gateway 10.67.2.1
```

- MyIta : Load Balancer
```
auto eth0
iface eth0 inet static
	address 10.67.2.3
	netmask 255.255.255.0
	gateway 10.67.2.1
```

- Serverny : Web Server
```
auto eth0
iface eth0 inet static
	address 10.67.1.2
	netmask 255.255.255.0
	gateway 10.67.1.1
```

- Stalber : Web Server
```
auto eth0
iface eth0 inet static
	address 10.67.1.3
	netmask 255.255.255.0
	gateway 10.67.1.1
```

- Lipovka : Web Server
```
auto eth0
iface eth0 inet static
	address 10.67.1.4
	netmask 255.255.255.0
	gateway 10.67.1.1
```