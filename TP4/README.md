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

```
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


- en ajoutant une route vers les réseaux, ils peuvent se ping entre eux
  - ajoutez une route par défaut sur les VPCS
  - ajoutez une route par défaut sur la machine virtuelle
  - testez des `ping` entre les réseaux
```