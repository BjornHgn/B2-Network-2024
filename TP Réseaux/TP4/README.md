# I. Topo 1 : VLAN et Routing

🌞 **Adressage**

```Powershell
PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.1.10.1/24
GATEWAY     : 255.255.255.0

NAME        : PC2[1]
IP/MASK     : 10.1.10.2/24
GATEWAY     : 255.255.255.0

NAME        : admin1[1]
IP/MASK     : 10.1.20.1/24
GATEWAY     : 255.255.255.0
```


🌞 **Configuration des VLANs**

```Powershell
10   clients                          active    Et0/0, Et0/1
20   admins                           active    Et0/2
30   servers                          active    Et0/3

Port        Mode             Encapsulation  Status        Native vlan
Et1/0       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et1/0       1-4094

Port        Vlans allowed and active in management domain
Et1/0       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Et1/0       1,10,20,30
```



🌞 **Config du *routeur***

```Powershell
R1(config)#interface fastEthernet 0/0.10
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)#ip addr 10.1.10.254 255.255.255.0
R1(config-subif)#exit
R1(config)#interface fastEthernet 0/0.20
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip addr 10.1.20.254 255.255.255.0
R1(config-subif)#exit
R1(config)#interface fastEthernet 0/0.30
R1(config-subif)#encapsulation dot1Q 30
R1(config-subif)#ip addr 10.1.30.254 255.255.255.0
R1(config-subif)#exit
R1(config)#exit
```

🌞 **Vérif**

```Powershell
admin1> ping 10.1.20.254

10.1.20.254 icmp_seq=1 timeout
84 bytes from 10.1.20.254 icmp_seq=2 ttl=255 time=51.983 ms
84 bytes from 10.1.20.254 icmp_seq=3 ttl=255 time=63.386 ms

PC1> ping 10.1.10.254

84 bytes from 10.1.10.254 icmp_seq=1 ttl=255 time=121.263 ms
84 bytes from 10.1.10.254 icmp_seq=2 ttl=255 time=11.626 ms

PC2> ping 10.1.10.254

84 bytes from 10.1.10.254 icmp_seq=1 ttl=255 time=7.621 ms
84 bytes from 10.1.10.254 icmp_seq=2 ttl=255 time=30.080 ms
```

# II. NAT

## 3. Setup topologie 2

🌞 **Ajoutez le noeud Cloud à la topo**

- branchez à `eth1` côté Cloud

```Powershell
R1(config)#interface FastEthernet1/0
R1(config-if)#ip address dhcp
R1(config-if)#no shut
R1(config-if)#

FastEthernet1/0            10.0.3.16       YES DHCP   up                    up 
```

```Powershell
R1#ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 60/62/64 ms
```


🌞 **Configurez le NAT**

```Powershell
R1(config)#interface FastEthernet1/0
R1(config-if)#ip nat outside

*Oct 16 10:13:53.735: %LINEPROTO-5-UPDOWN: Line protocol on Interface NVI0, changed state to up
R1(config-if)#

R1(config-if)#ip nat outside
R1(config-if)#exit
R1(config)#interface FastEthernet0/0
R1(config-if)#ip nat inside
R1(config-if)#exit
R1(config)#access-list 1 permit any
R1(config)#ip nat inside source list 1 interface FastEthernet1/0 overload
R1(config)#exit
```

🌞 **Test**

```Powershell
PC2> show ip

NAME        : PC2[1]
IP/MASK     : 10.1.10.2/24
GATEWAY     : 10.1.10.254

admin1> show ip

NAME        : admin1[1]
IP/MASK     : 10.1.20.1/24
GATEWAY     : 10.1.20.254

PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.1.10.1/24
GATEWAY     : 10.1.10.254
```

```Powershell
NAME   IP/MASK              GATEWAY           MAC                DNS
admin1 10.1.20.1/24         10.1.20.254       00:50:79:66:68:02  1.1.1.1

admin1> ping google.com
google.com resolved to 142.251.37.174

84 bytes from 142.251.37.174 icmp_seq=1 ttl=111 time=39.994 ms
84 bytes from 142.251.37.174 icmp_seq=2 ttl=111 time=30.912 ms

PC2> ping google.com
google.com resolved to 142.250.200.206

84 bytes from 142.250.200.206 icmp_seq=1 ttl=112 time=30.244 ms

PC1> ping google.com
google.com resolved to 142.250.200.206

84 bytes from 142.250.200.206 icmp_seq=1 ttl=112 time=52.022 ms
```

# III. Add a building

🌞  **Vous devez me rendre le `show running-config` de tous les équipements**

[Conf Routeur](conf_R1.md)

[Conf Routeur](conf_sw2.md)

[Conf Routeur](conf_sw3.md)

[Conf Routeur](conf_sw1.md)