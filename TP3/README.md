# TP3 INFRA : Premiers pas GNS, Cisco et VLAN

🌞 **Commençons simple**

```Powershell
PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.3.1.1/24
GATEWAY     : 255.255.255.0
DNS         :
MAC         : 00:50:79:66:68:00
LPORT       : 20004
RHOST:PORT  : 127.0.0.1:20005
MTU         : 1500
```


```Powershell
PC2> sho ip

NAME        : PC2[1]
IP/MASK     : 10.3.1.2/24
GATEWAY     : 255.255.255.0
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 20006
RHOST:PORT  : 127.0.0.1:20007
MTU         : 1500

PC2> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=64 time=2.831 ms
84 bytes from 10.3.1.1 icmp_seq=2 ttl=64 time=2.602 ms
^C
```

```Powrshell
IOU1#show mac address-table

          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0050.7966.6800    DYNAMIC     Et0/0
   1    0050.7966.6801    DYNAMIC     Et0/1
```

# II. VLAN

### 3. Setup topologie 2

🌞 **Adressage**

```Powershell
PC3> ip 10.3.1.3 255.255.255.0
Checking for duplicate address...
PC3 : 10.3.1.3 255.255.255.0

PC3> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=64 time=2.831 ms
84 bytes from 10.3.1.1 icmp_seq=2 ttl=64 time=2.602 ms
```

🌞 **Configuration des VLANs**

IOU1#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/3, Et1/0, Et1/1, Et1/2
                                                Et1/3, Et2/0, Et2/1, Et2/2
                                                Et2/3, Et3/0, Et3/1, Et3/2
                                                Et3/3
10   vlan10                           active    Et0/0, Et0/1
20   vlan20                           active    Et0/2

🌞 **Vérif**

```Powershell
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=3.187 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=2.560 ms
84 bytes from 10.3.1.2 icmp_seq=3 ttl=64 time=2.812 ms

PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=73.950 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=1.749 ms

PC1> ping 10.3.1.3

host (10.3.1.3) not reachable

PC3> ping 10.1.3.2

host (255.255.255.0) not reachable

```


# III. Ptite VM DHCP

On va ajouter une VM dans la topologie, histoire que vous voyiez cet aspect de GNS.

| Node          | IP              | VLAN |
| ------------- | --------------- | ---- |
| `pc1`         | `10.3.1.1/24`   | 10   |
| `pc2`         | `10.3.1.2/24`   | 10   |
| `pc3`         | `10.3.1.3/24`   | 20   |
| `pc4`         | X               | 20   |
| `pc5`         | X               | 10   |
| `dhcp.tp3.b2` | `10.3.1.253/24` | 20   |

🌞 **VM `dhcp.tp3.b2`**

```Powershell
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

option domain-name-servers     dlp.srv.world;
# default lease time

default-lease-time 600;
# max lease time

max-lease-time 7200;
# this DHCP server to be declared valid

authoritative;
# specify network address and subnetmask

subnet 10.3.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.3.1.100 10.3.1.200;
    # specify broadcast address
    option broadcast-address 10.3.1.255;
    # specify gateway
}
```

```Powershell
PC4> dhcp -r
DDORA
PC4> show IP 10.3.1.100/24
No router answered ICMPv6 Router Solicitation
```

```Powershell
# authoring-byte-order entry is generated, DO NOT DELETE
authoring-byte-order little-endian;

server-duid "\000\001\000\001.\232o>\010\000'\2240\202";

lease 10.3.1.100 {
  starts 4 2024/10/10 11:01:28;
  ends 4 2024/10/10 11:11:28;
  cltt 4 2024/10/10 11:01:28;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 00:50:79:66:68:03;
  uid "\001\000Pyfh\003";
  client-hostname "PC4";
}
```

```Powershell
PC5> dhcp -r
DDD
Can't find dhcp server
```
