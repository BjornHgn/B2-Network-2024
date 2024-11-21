```Shell
hostname R2

interface FastEthernet0/0
 ip address 10.6.21.2 255.255.255.252
 duplex half
!
interface FastEthernet1/0
 ip address 10.6.23.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet1/1
 ip address 10.6.52.2 255.255.255.252
 duplex auto
 speed auto
!
router ospf 1
 router-id 2.2.2.2
 log-adjacency-changes
 network 10.6.21.0 0.0.0.3 area 0
 network 10.6.23.0 0.0.0.3 area 0
 network 10.6.52.0 0.0.0.3 area 1

end

```

```shell
R2#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
3.3.3.3           1   FULL/BDR        00:00:34    10.6.23.1       FastEthernet1/0
1.1.1.1           1   FULL/BDR        00:00:31    10.6.21.1       FastEthernet0/0
5.5.5.5           1   FULL/BDR        00:00:36    10.6.52.1       FastEthernet1/1



R2#sho ip route

Gateway of last resort is 10.6.52.1 to network 0.0.0.0

O    192.168.172.0/24 [110/2] via 10.6.52.1, 00:02:22, FastEthernet1/1
     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O       10.6.13.0/30 [110/2] via 10.6.23.1, 00:07:56, FastEthernet1/0
                     [110/2] via 10.6.21.1, 00:07:56, FastEthernet0/0
O IA    10.6.1.0/24 [110/3] via 10.6.21.1, 00:02:22, FastEthernet0/0
O IA    10.6.2.0/24 [110/3] via 10.6.21.1, 00:02:22, FastEthernet0/0
O IA    10.6.3.0/24 [110/2] via 10.6.21.1, 00:02:22, FastEthernet0/0
C       10.6.21.0/30 is directly connected, FastEthernet0/0
C       10.6.23.0/30 is directly connected, FastEthernet1/0
O IA    10.6.41.0/30 [110/2] via 10.6.21.1, 00:02:22, FastEthernet0/0
C       10.6.52.0/30 is directly connected, FastEthernet1/1
O*E2 0.0.0.0/0 [110/1] via 10.6.52.1, 00:02:22, FastEthernet1/1
```
