# TP2 : Environnement virtuel

## Compte-rendu

☀️ Sur **`node1.lan1.tp2`**

```Powershell
[bjorn@node1 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:88:36:34 brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.11/24 brd 10.1.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe88:3634/64 scope link
       valid_lft forever preferred_lft forever
```

```Powershell
[bjorn@node1 ~]$ ip r s
10.1.1.0/24 dev enp0s3 proto kernel scope link src 10.1.1.11 metric 100
```

```Powershell
[bjorn@node1 ~]$ ping 10.1.2.12
PING 10.1.2.12 (10.1.2.12) 56(84) bytes of data.
64 bytes from 10.1.2.12: icmp_seq=1 ttl=63 time=3.88 ms
64 bytes from 10.1.2.12: icmp_seq=2 ttl=63 time=2.37 ms
64 bytes from 10.1.2.12: icmp_seq=3 ttl=63 time=2.82 ms
```

```Powershell
[bjorn@node1 ~]$ traceroute 10.1.2.12
traceroute to 10.1.2.12 (10.1.2.12), 30 hops max, 60 byte packets
 1  10.1.1.254 (10.1.1.254)  4.853 ms  4.545 ms  3.851 ms
 2  10.1.2.12 (10.1.2.12)  2.843 ms !X  4.631 ms !X  4.542 ms !X
```

# II. Interlude accès internet


☀️ **Sur `router.tp2`**

```Powershell
[bjorn@RouteurTP2 ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=113 time=35.7 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=113 time=20.6 ms
[bjorn@RouteurTP2 ~]$ ping www.google.com
PING www.google.com (142.250.179.100) 56(84) bytes of data.
64 bytes from par21s20-in-f4.1e100.net (142.250.179.100): icmp_seq=1 ttl=116 time=21.5 ms
64 bytes from par21s20-in-f4.1e100.net (142.250.179.100): icmp_seq=2 ttl=116 time=20.7 ms
```

☀️ **Accès internet LAN1 et LAN2**

```Powershell
[bjorn@node2 ~]$ ip r s
default via 10.1.1.254 dev enp0s3 proto static metric 100

[bjorn@node2 ~]$ sudo cat /etc/resolv.conf
nameserver 1.1.1.1

[bjorn@node2 ~]$ ping google.com
PING google.com (142.250.201.14) 56(84) bytes of data.
64 bytes from mrs08s19-in-f14.1e100.net (142.250.201.14): icmp_seq=1 ttl=112 time=21.8 ms
64 bytes from mrs08s19-in-f14.1e100.net (142.250.201.14): icmp_seq=2 ttl=112 time=21.7 ms
64 bytes from mrs08s19-in-f14.1e100.net (142.250.201.14): icmp_seq=3 ttl=112 time=20.5 ms

[bjorn@node2 ~]$ ping 142.251.37.238
PING 142.251.37.238 (142.251.37.238) 56(84) bytes of data.
64 bytes from 142.251.37.238: icmp_seq=1 ttl=111 time=22.4 ms
64 bytes from 142.251.37.238: icmp_seq=2 ttl=111 time=22.7 ms
64 bytes from 142.251.37.238: icmp_seq=3 ttl=111 time=28.2 ms
```

# III. Services réseau

☀️ **Sur `web.lan2.tp2`**

```Powershell
[bjorn@web ~]$ sudo dnf install nginx

[bjorn@web ~]$ sudo nano /var/www/site_nul/index.html

[bjorn@web ~]$ sudo chown -R $USER:$USER /var/www/site_nul

[bjorn@web ~]$ sudo systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: disabled)
     Active: active (running) since Fri 2024-10-04 11:15:58 CEST; 5min ago
sudo firewall-cmd --add-port=80/tcp --permanent

[bjorn@web ~]$ ss -ltun | grep 80
tcp   LISTEN 0      511          0.0.0.0:80        0.0.0.0:*
tcp   LISTEN 0      511             [::]:80           [::]:*

[bjorn@web ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3
  sources:
  services: cockpit dhcpv6-client http ssh
  ports: 80/tcp

```

☀️ **Sur `node1.lan1.tp2`**

```Powershell
[bjorn@node1 ~]$ sudo cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.1.2.12       site_nul.tp2

[bjorn@node1 ~]$ curl site_nul.tp2
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.20.1</center>
</body>
</html>
```

## 2. Or is it ? Bonus : DHCP

☀️ **Sur `dhcp.lan1.tp2`**

```Powershell
[bjorn@dhcp ~]$ sudo cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
[sudo] password for bjorn:
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.1.1.253
NETMASK=255.255.255.0

[bjorn@dhcp ~]$ sudo dnf install -y dhcp-server

[bjorn@dhcp ~]$ sudo cat /etc/dhcp/dhcpd.conf
# specify domain name

option domain-name     "srv.world";
# specify DNS server's hostname or IP address

option domain-name-servers     1.1.1.1;
# default lease time

default-lease-time 600;
# max lease time

max-lease-time 7200;
# this DHCP server to be declared valid

authoritative;
# specify network address and subnetmask

subnet 10.1.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.1.1.100 10.1.1.200;
    # specify gateway
    option routers 10.1.1.254;
}

[bjorn@dhcp ~]$ sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; disabled; preset: disabled)
     Active: active (running) since Fri 2024-10-04 11:50:14 CEST; 1min 29s ago
```
☀️ **Sur `node1.lan1.tp2`**

```Powershell
[bjorn@node1 ~]$ sudo dhclient

[bjorn@node1 ~]$ ip a show dev enp0s3
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:88:36:34 brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.11/24 brd 10.1.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet 10.1.1.100/24 brd 10.1.1.255 scope global secondary dynamic enp0s3
       valid_lft 639sec preferred_lft 639sec

[bjorn@node1 ~]$ ip route
default via 10.1.1.254 dev enp0s3

[bjorn@node1 ~]$ ping 10.1.2.12
PING 10.1.2.12 (10.1.2.12) 56(84) bytes of data.
64 bytes from 10.1.2.12: icmp_seq=1 ttl=63 time=2.89 ms
64 bytes from 10.1.2.12: icmp_seq=2 ttl=63 time=5.69 ms
```
