```Shell
hostname R4

interface FastEthernet0/0
 ip address 10.6.1.254 255.255.255.0
 duplex half
!
interface FastEthernet1/0
 ip address 10.6.2.254 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet1/1
 ip address 10.6.41.2 255.255.255.252
 duplex auto
 speed auto
!
router ospf 1
 router-id 4.4.4.4
 log-adjacency-changes
 network 10.6.1.0 0.0.0.255 area 3
 network 10.6.2.0 0.0.0.255 area 3
 network 10.6.41.0 0.0.0.3 area 3
!
end
```

```shell
R4#sho ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/BDR        00:00:34    10.6.41.1       FastEthernet1/1



R4#sho ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is 10.6.41.1 to network 0.0.0.0

O IA 192.168.172.0/24 [110/4] via 10.6.41.1, 00:04:30, FastEthernet1/1
     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O IA    10.6.13.0/30 [110/2] via 10.6.41.1, 00:11:48, FastEthernet1/1
C       10.6.1.0/24 is directly connected, FastEthernet0/0
C       10.6.2.0/24 is directly connected, FastEthernet1/0
O IA    10.6.3.0/24 [110/2] via 10.6.41.1, 00:11:38, FastEthernet1/1
O IA    10.6.21.0/30 [110/2] via 10.6.41.1, 00:11:56, FastEthernet1/1
O IA    10.6.23.0/30 [110/3] via 10.6.41.1, 00:11:48, FastEthernet1/1
C       10.6.41.0/30 is directly connected, FastEthernet1/1
O IA    10.6.52.0/30 [110/3] via 10.6.41.1, 00:11:48, FastEthernet1/1
O*E2 0.0.0.0/0 [110/1] via 10.6.41.1, 00:04:25, FastEthernet1/1
```

```shell
R4#sho ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/BDR        00:00:34    10.6.41.1       FastEthernet1/1


R4#sho ip route

Gateway of last resort is 10.6.41.1 to network 0.0.0.0

O IA 192.168.172.0/24 [110/4] via 10.6.41.1, 00:04:30, FastEthernet1/1
     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O IA    10.6.13.0/30 [110/2] via 10.6.41.1, 00:11:48, FastEthernet1/1
C       10.6.1.0/24 is directly connected, FastEthernet0/0
C       10.6.2.0/24 is directly connected, FastEthernet1/0
O IA    10.6.3.0/24 [110/2] via 10.6.41.1, 00:11:38, FastEthernet1/1
O IA    10.6.21.0/30 [110/2] via 10.6.41.1, 00:11:56, FastEthernet1/1
O IA    10.6.23.0/30 [110/3] via 10.6.41.1, 00:11:48, FastEthernet1/1
C       10.6.41.0/30 is directly connected, FastEthernet1/1
O IA    10.6.52.0/30 [110/3] via 10.6.41.1, 00:11:48, FastEthernet1/1
O*E2 0.0.0.0/0 [110/1] via 10.6.41.1, 00:04:25, FastEthernet1/1
```