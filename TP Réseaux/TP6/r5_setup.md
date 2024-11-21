```Shell
hostname R5

interface FastEthernet0/0
 ip address 10.6.52.1 255.255.255.252
 ip nat inside
 ip virtual-reassembly
 duplex half
!
interface FastEthernet1/0
 ip address dhcp
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
router ospf 1
 router-id 5.5.5.5
 log-adjacency-changes
 network 10.6.52.0 0.0.0.3 area 1
 network 192.168.172.0 0.0.0.255 area 1
 default-information originate always

end

```

```shell
R5#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:39    10.6.52.2       FastEthernet0/0


R5#sho ip route

Gateway of last resort is not set

C    192.168.172.0/24 is directly connected, FastEthernet1/0
     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O IA    10.6.13.0/30 [110/3] via 10.6.52.2, 00:03:18, FastEthernet0/0
O IA    10.6.1.0/24 [110/4] via 10.6.52.2, 00:03:18, FastEthernet0/0
O IA    10.6.2.0/24 [110/4] via 10.6.52.2, 00:03:18, FastEthernet0/0
O IA    10.6.3.0/24 [110/3] via 10.6.52.2, 00:03:18, FastEthernet0/0
O IA    10.6.21.0/30 [110/2] via 10.6.52.2, 00:03:18, FastEthernet0/0
O IA    10.6.23.0/30 [110/2] via 10.6.52.2, 00:03:18, FastEthernet0/0
O IA    10.6.41.0/30 [110/3] via 10.6.52.2, 00:03:18, FastEthernet0/0
C       10.6.52.0/30 is directly connected, FastEthernet0/0
```
