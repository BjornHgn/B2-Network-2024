```shell
hostname R3

interface FastEthernet0/0
 ip address 10.6.23.1 255.255.255.252
 duplex half
!
interface FastEthernet1/0
 ip address 10.6.13.2 255.255.255.252
 duplex auto
 speed auto

router ospf 1
 router-id 3.3.3.3
 log-adjacency-changes
 network 10.6.13.0 0.0.0.3 area 0
 network 10.6.23.0 0.0.0.3 area 0
!
ip forward-protocol nd
!
end
```

```shell
R3#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/DR         00:00:32    10.6.13.1   FastEthernet1/                                                                                                             0
2.2.2.2           1   FULL/DR         00:00:34    10.6.23.2       FastEthernet0/                                                                                                             0

R3#show ip route


Gateway of last resort is 10.6.23.2 to network 0.0.0.0

O IA 192.168.172.0/24 [110/3] via 10.6.23.2, 00:08:10, FastEthernet0/0
     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
C       10.6.13.0/30 is directly connected, FastEthernet1/0
O IA    10.6.1.0/24 [110/3] via 10.6.13.1, 00:13:45, FastEthernet1/0
O IA    10.6.2.0/24 [110/3] via 10.6.13.1, 00:13:45, FastEthernet1/0
O IA    10.6.3.0/24 [110/2] via 10.6.13.1, 00:13:45, FastEthernet1/0
O       10.6.21.0/30 [110/2] via 10.6.23.2, 00:13:45, FastEthernet0/0
                     [110/2] via 10.6.13.1, 00:13:45, FastEthernet1/0
C       10.6.23.0/30 is directly connected, FastEthernet0/0
O IA    10.6.41.0/30 [110/2] via 10.6.13.1, 00:13:45, FastEthernet1/0
O IA    10.6.52.0/30 [110/2] via 10.6.23.2, 00:13:45, FastEthernet0/0
O*E2 0.0.0.0/0 [110/1] via 10.6.23.2, 00:08:05, FastEthernet0/0
```
