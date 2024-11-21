# TP6 INFRA : STP, OSPF, bigger infra

## I. STP

### 1. Basic setup

🌞 **Configurer STP sur les 3 switches**

```Shell
IOU3#sh spanning-tree

Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Root FWD 100       128.2    P2p
Et0/2               Altn BLK 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p

IOU2#sh spanning-tree
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Root FWD 100       128.2    P2p
Et0/2               Desg FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p

IOU1#show spanning-tree
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Desg FWD 100       128.2    P2p
Et0/2               Desg FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
```

🌞 **Altérer le spanning-tree** en désactivant un port

```Shell
IOU1#show spanning-tree
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Desg FWD 100       128.2    P2p
Et0/2               Root FWD 100       128.3    P2p

IOU2#sh spanning-tree
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Root FWD 100       128.2    P2p
Et0/2               Desg FWD 100       128.3    P2p
```

🌞 **Altérer le spanning-tree** en modifiant le coût d'un lien

```Shell
IOU1#conf t
IOU1(config)#interface ethernet 0/2
IOU1(config-if)#spanning-tree cost 10

Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Desg FWD 100       128.2    P2p
Et0/2               Desg FWD 10        128.3    P2p
```

🦈 **`tp6_stp.pcapng`**

[Capture traffic STP](tp6_stp.pcapng)

## II. OSPF

🌞 **Montez la topologie**

```Shell
PC4> sh ip

NAME        : PC4[1]
IP/MASK     : 10.6.3.11/24
GATEWAY     : 10.6.3.254

PC2> sh ip

NAME        : PC2[1]
IP/MASK     : 10.6.2.11/24
GATEWAY     : 10.6.2.254

PC1> sh ip

NAME        : PC1[1]
IP/MASK     : 10.6.1.11/24
GATEWAY     : 10.6.1.254
```

🌞 **Configurer OSPF sur tous les routeurs**

[Setup Routeur 5](r5_setup.md)
[Setup Routeur 4](r4_setup.md)
[Setup Routeur 3](r3_setup.md)
[Setup Routeur 2](r2_setup.md)
[Setup Routeur 1](r1_setup.md)

🌞 **Test**

```shell
PC1> ping 10.6.3.11

84 bytes from 10.6.3.11 icmp_seq=1 ttl=62 time=30.690 ms
84 bytes from 10.6.3.11 icmp_seq=2 ttl=62 time=35.512 ms
84 bytes from 10.6.3.11 icmp_seq=3 ttl=62 time=31.153 ms

PC2> ping 10.6.3.11

10.6.3.11 icmp_seq=1 timeout
84 bytes from 10.6.3.11 icmp_seq=2 ttl=62 time=35.845 ms
84 bytes from 10.6.3.11 icmp_seq=3 ttl=62 time=44.872 ms
84 bytes from 10.6.3.11 icmp_seq=4 ttl=62 time=39.480 ms
```

🦈 **`tp6_ospf.pcapng`**

[Capture traffic OSPF](tp6_ospf.pcapng)

## III. DHCP relay

➜ **Un problème très récurrent pour pas dire omniprésent avec DHCP c'est que ça marche que dans un LAN.**

Si t'as un serveur DHCP, et plein de réseaux comme c'est le cas ici, c'est le bordel :

- un DHCP Request, qui part en broadcast ne passe pas un routeur
  - il est cantonné au LAN
- en effet, pour changer de réseau, il faut construire des paquets IP
  - or, un DHCP request, c'est juste une trame Ethernet, pas de paquet IP dedans
- et donc, quand tu fais ton DHCP Request c'est ça que tu cherches : avoir une IP
- dans notre topo actuelle, impossible que John contacte le serveur DHCP

➜ **DHCP Relay !**

- on va demander à un routeur, s'il reçoit des trames DHCP de les faire passer vers notre serveur DHCP
- si le serveur DHCP le supporte, il répondra donc au routeur, qui fera passer au client

> *Spoiler alert, le serveur qu'on utilise depuis l'an dernier, fourni dans les dépôts Rocky, le supporte.*

🌞 **Configurer un serveur DHCP** sur `dhcp.tp6.b1`

```shell
[bjorn@dhcp ~]$ sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Thu 2024-11-21 11:31:32 CET; 9s ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 1747 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 17917)
     Memory: 4.6M
        CPU: 37ms
     CGroup: /system.slice/dhcpd.service
             └─1747 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid

Nov 21 11:31:32 dhcp.tp3.b2 dhcpd[1747]: ** Ignoring requests on enp0s8.  If this is not what
Nov 21 11:31:32 dhcp.tp3.b2 dhcpd[1747]:    you want, please write a subnet declaration
Nov 21 11:31:32 dhcp.tp3.b2 dhcpd[1747]:    in your dhcpd.conf file for the network segment
Nov 21 11:31:32 dhcp.tp3.b2 dhcpd[1747]:    to which interface enp0s8 is attached. **
Nov 21 11:31:32 dhcp.tp3.b2 dhcpd[1747]:
Nov 21 11:31:32 dhcp.tp3.b2 dhcpd[1747]: Listening on LPF/enp0s3/08:00:27:e5:3d:a9/10.6.1.0/24
Nov 21 11:31:32 dhcp.tp3.b2 dhcpd[1747]: Sending on   LPF/enp0s3/08:00:27:e5:3d:a9/10.6.1.0/24
Nov 21 11:31:32 dhcp.tp3.b2 dhcpd[1747]: Sending on   Socket/fallback/fallback-net
Nov 21 11:31:32 dhcp.tp3.b2 dhcpd[1747]: Server starting service.
Nov 21 11:31:32 dhcp.tp3.b2 systemd[1]: Started DHCPv4 Server Daemon.
```

```shell
[bjorn@dhcp ~]$ sudo cat /etc/dhcp/dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
# create new

# specify domain name

option domain-name     "srv.world";
# specify DNS server's hostname or IP address

option domain-name-servers  1.1.1.1;

# default lease time

default-lease-time 600;
# max lease time

max-lease-time 7200;
# this DHCP server to be declared valid

authoritative;
# specify network address and subnetmask

subnet 10.6.1.0 netmask 255.255.255.0 {
    range dynamic-bootp 10.6.1.100 10.6.1.200;
    option broadcast-address 10.6.1.255;
    option routers 10.6.1.254;
}


subnet 10.6.3.0 netmask 255.255.255.0 {
    range dynamic-bootp 10.6.3.100 10.6.3.200;
    option broadcast-address 10.6.3.255;
    option routers 10.6.3.254;
}
```

🌞 **Configurer un DHCP relay sur la passerelle de John**

```shell
PC1> ip dhcp
DDORA IP 10.6.1.100/24 GW 10.6.1.254
```

```shell
PC4> ip dhcp
DDORA IP 10.6.3.100/24 GW 10.6.3.254
```

```shell
[bjorn@dhcp ~]$ cat /var/lib/dhcpd/dhcpd.leases
# The format of this file is documented in the dhcpd.leases(5) manual page.
# This lease file was written by isc-dhcp-4.4.2b1

# authoring-byte-order entry is generated, DO NOT DELETE
authoring-byte-order little-endian;

server-duid "\000\001\000\001.\232o>\010\000'\2240\202";

lease 10.6.1.100 {
  starts 4 2024/11/21 10:37:29;
  ends 4 2024/11/21 10:47:29;
  cltt 4 2024/11/21 10:37:29;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 00:50:79:66:68:00;
  uid "\001\000Pyfh\000";
  client-hostname "PC1";
}
lease 10.6.3.100 {
  starts 4 2024/11/21 10:41:24;
  ends 4 2024/11/21 10:51:24;
  cltt 4 2024/11/21 10:41:24;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 00:50:79:66:68:02;
  uid "\001\000Pyfh\002";
  client-hostname "PC4";
}
```
