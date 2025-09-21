## Лабораторная работа №5 по теме: "VxLAN. EVPN L2"
___
### Цель:
Настроить Overlay на основе VxLAN EVPN для L2 связанности между клиентами.

### Задание:
1. Настроите BGP peering между Leaf и Spine в AF l2vpn evpn.
2. Настроите связанность между клиентами в первой зоне и убедитесь в её наличии
3. Зафиксируете в документации - план работы, адресное пространство, схему сети, конфигурацию устройств
___

## Выполнение:

## Схема сети
![Picture background](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab05/mytopology.jpg)


## BGP peering между Leaf и Spine в AF l2vpn evpn

Leaf1#show bgp evpn summary
![Picture background](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab05/bgp_peer1.jpg)

Spine1#show bgp evpn summary
![Picture background](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab05/bgp_peer2.jpg)


| Loopback0 | adrss | 
------ | ------ |
Пул: | 10.255.0.0/20 |
SP1: | 10.255.0.1/32 | 
SP2: | 10.255.0.2/32 |
LE1: | 10.255.1.1/32 |
LE2: |10.255.1.2/32 |
LE3: |10.255.1.3/32 |

--------------------------

| P2P-линки | adrss | линки |
------ | ------ | ----- |
Для S1 | 10.0.0.0/25 |
S1-L1 | 10.0.0.0/31 | S1=10.0.0.0 - L1=10.0.0.1 |
S1–L2 | 10.0.0.2/31 | S1=10.0.0.2 - L2=10.0.0.3 |
S1–L3 | 10.0.0.4/31 | S1=10.0.0.4 - L3=10.0.0.5 |
Для S2 | 10.0.0.128/25 |
S2-L1 | 10.0.0.128/31 | S2=10.0.0.128 - L1=10.0.0.129 |
S2–L2 | 10.0.0.130/31 | S2=10.0.0.130 - L2=10.0.0.131 |
S2–L3 | 10.0.0.132/31 | S2=10.0.0.132 - L3=10.0.0.133|
-----------------------------

## Конфигурация устройств


<details> 

<summary> Leaf1 </summary>

```
!
no aaa root
!
no service interface inactive port-id allocation disabled
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf1
!
spanning-tree mode mstp
!
system l1
   unsupported speed action error
   unsupported error-correction action error
!
interface Ethernet1
   mtu 9194
   no switchport
   ip address 10.0.0.1/31
!
interface Ethernet2
   mtu 9194
   no switchport
   ip address 10.0.0.129/31
!
interface Ethernet3
   no switchport
   ip address 192.168.1.1/24
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.255.1.1/32
!
interface Management1
!
ip routing
!
router bgp 65500
   router-id 10.255.1.1
   maximum-paths 2 ecmp 2
   neighbor UNDERLAY peer group
   neighbor UNDERLAY remote-as 65500
   neighbor UNDERLAY bfd
   neighbor 10.0.0.0 peer group UNDERLAY
   neighbor 10.0.0.128 peer group UNDERLAY
   network 10.255.1.1/32
   network 192.168.1.0/24
!
router multicast
   ipv4
      software-forwarding kernel
   !
   ipv6
      software-forwarding kernel
!
end


```
</details>
<details> 

<summary> Leaf2  </summary>

```
!
no aaa root
!
no service interface inactive port-id allocation disabled
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf2
!
spanning-tree mode mstp
!
system l1
   unsupported speed action error
   unsupported error-correction action error
!
interface Ethernet1
   mtu 9194
   no switchport
   ip address 10.0.0.3/31
!
interface Ethernet2
   mtu 9194
   no switchport
   ip address 10.0.0.131/31
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.255.1.2/32
!
interface Management1
!
ip routing
!
router bgp 65500
   router-id 10.255.1.2
   neighbor UNDERLAY peer group
   neighbor UNDERLAY remote-as 65500
   neighbor UNDERLAY bfd
   neighbor 10.0.0.2 peer group UNDERLAY
   neighbor 10.0.0.130 peer group UNDERLAY
   network 10.255.1.2/32
!
router multicast
   ipv4
      software-forwarding kernel
   !
   ipv6
      software-forwarding kernel
!
end


```
</details>
<details> 

<summary> Leaf3 </summary>

```
!
no aaa root
!
no service interface inactive port-id allocation disabled
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf3
!
spanning-tree mode mstp
!
system l1
   unsupported speed action error
   unsupported error-correction action error
!
interface Ethernet1
   mtu 9194
   no switchport
   ip address 10.0.0.5/31
!
interface Ethernet2
   mtu 9194
   no switchport
   ip address 10.0.0.133/31
!
interface Ethernet3
   no switchport
   ip address 192.168.3.1/25
!
interface Ethernet4
   no switchport
   ip address 192.168.3.129/25
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.255.1.3/32
!
interface Management1
!
ip routing
!
router bgp 65500
   router-id 10.255.1.3
   neighbor UNDERLAY peer group
   neighbor UNDERLAY remote-as 65500
   neighbor UNDERLAY bfd
   neighbor 10.0.0.4 peer group UNDERLAY
   neighbor 10.0.0.132 peer group UNDERLAY
   network 10.255.1.3/32
   network 192.168.3.128/25
!
router multicast
   ipv4
      software-forwarding kernel
   !
   ipv6
      software-forwarding kernel
!
end


```
</details>
<details> 
<summary> Spine 1 </summary>


```
!
no aaa root
!
no service interface inactive port-id allocation disabled
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine1
!
spanning-tree mode mstp
!
system l1
   unsupported speed action error
   unsupported error-correction action error
!
interface Ethernet1
   mtu 9194
   no switchport
   ip address 10.0.0.0/31
!
interface Ethernet2
   mtu 9194
   no switchport
   ip address 10.0.0.2/31
!
interface Ethernet3
   mtu 9194
   no switchport
   ip address 10.0.0.4/31
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.255.0.1/32
!
interface Management1
!
ip routing
!
router bgp 65500
   router-id 10.255.0.1
   neighbor UNDERLAY peer group
   neighbor UNDERLAY remote-as 65500
   neighbor UNDERLAY next-hop-self
   neighbor UNDERLAY bfd
   neighbor UNDERLAY route-reflector-client
   neighbor 10.0.0.1 peer group UNDERLAY
   neighbor 10.0.0.3 peer group UNDERLAY
   neighbor 10.0.0.5 peer group UNDERLAY
   network 10.255.0.1/32
!
router multicast
   ipv4
      software-forwarding kernel
   !
   ipv6
      software-forwarding kernel
!
end


```
</details>
<details> 
<summary> Spine 2 </summary>


```
!
no aaa root
!
no service interface inactive port-id allocation disabled
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine2
!
spanning-tree mode mstp
!
system l1
   unsupported speed action error
   unsupported error-correction action error
!
interface Ethernet1
   mtu 9194
   no switchport
   ip address 10.0.0.128/31
!
interface Ethernet2
   mtu 9194
   no switchport
   ip address 10.0.0.130/31
!
interface Ethernet3
   mtu 9194
   no switchport
   ip address 10.0.0.132/31
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.255.0.2/32
!
interface Management1
!
ip routing
!
router bgp 65500
   router-id 10.255.0.2
   neighbor UNDERLAY peer group
   neighbor UNDERLAY remote-as 65500
   neighbor UNDERLAY next-hop-self
   neighbor UNDERLAY bfd
   neighbor UNDERLAY route-reflector-client
   neighbor 10.0.0.129 peer group UNDERLAY
   neighbor 10.0.0.131 peer group UNDERLAY
   neighbor 10.0.0.133 peer group UNDERLAY
   network 10.255.0.2/32
!
router multicast
   ipv4
      software-forwarding kernel
   !
   ipv6
      software-forwarding kernel
!
end



```
</details>

------------------------------------

Проверка IP связанности (между L1 и L3)
```
Leaf1#ping 10.255.1.3 source loopback 0
PING 10.255.1.3 (10.255.1.3) from 10.255.1.1 : 72(100) bytes of data.
80 bytes from 10.255.1.3: icmp_seq=1 ttl=63 time=26.2 ms
80 bytes from 10.255.1.3: icmp_seq=2 ttl=63 time=19.7 ms
80 bytes from 10.255.1.3: icmp_seq=3 ttl=63 time=26.9 ms
80 bytes from 10.255.1.3: icmp_seq=4 ttl=63 time=7.37 ms
80 bytes from 10.255.1.3: icmp_seq=5 ttl=63 time=14.5 ms

--- 10.255.1.3 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 79ms
rtt min/avg/max/mdev = 7.366/18.931/26.945/7.374 ms, pipe 3, ipg/ewma 19.863/22.216 ms


```
Trace от Leaf1 до Leaf3
```
Leaf1#traceroute 10.255.1.3 source loopback 0
traceroute to 10.255.1.3 (10.255.1.3), 30 hops max, 60 byte packets
 1  10.0.0.128 (10.0.0.128)  13.981 ms  24.185 ms  24.782 ms
 2  10.255.1.3 (10.255.1.3)  29.267 ms  36.954 ms  58.458 ms

```

Проверка наличия маршрутов

```
Leaf1#show ip route bgp

VRF: default
Source Codes:
       C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route,
       CL - CBF Leaked Route

 B I      10.255.0.1/32 [200/0]
           via 10.0.0.0, Ethernet1
 B I      10.255.0.2/32 [200/0]
           via 10.0.0.128, Ethernet2
 B I      10.255.1.2/32 [200/0]
           via 10.0.0.0, Ethernet1
           via 10.0.0.128, Ethernet2
 B I      10.255.1.3/32 [200/0]
           via 10.0.0.0, Ethernet1
           via 10.0.0.128, Ethernet2
 B I      192.168.3.128/25 [200/0]
           via 10.0.0.0, Ethernet1
           via 10.0.0.128, Ethernet2

```

Вывод BGP Detail

```
Leaf1#show ip bgp detail
BGP routing table information for VRF default
Router identifier 10.255.1.1, local AS number 65500
BGP routing table entry for 10.255.0.1/32
 Paths: 1 available
  Local
    10.0.0.0 from 10.0.0.0 (10.255.0.1)
      Origin IGP, metric 0, localpref 100, IGP metric 0, weight 0, tag 0
      Received 8d17h ago, valid, internal, best
      Rx SAFI: Unicast
BGP routing table entry for 10.255.0.2/32
 Paths: 1 available
  Local
    10.0.0.128 from 10.0.0.128 (10.255.0.2)
      Origin IGP, metric 0, localpref 100, IGP metric 0, weight 0, tag 0
      Received 8d17h ago, valid, internal, best
      Rx SAFI: Unicast
BGP routing table entry for 10.255.1.1/32
 Paths: 1 available
  Local
    - from - (10.255.1.1)
      Origin IGP, metric -, localpref -, IGP metric -, weight 0, tag 0
      Received 8d17h ago, valid, local, best, redistributed (Connected)
      Rx SAFI: Unicast
BGP routing table entry for 10.255.1.2/32
 Paths: 2 available
  Local
    10.0.0.128 from 10.0.0.128 (10.255.0.2)
      Origin IGP, metric 0, localpref 100, IGP metric 0, weight 0, tag 0
      Received 8d17h ago, valid, internal, ECMP head, ECMP, best, ECMP contributor
      Originator: 10.255.1.2, Cluster list: 10.255.0.2
      Rx SAFI: Unicast
  Local
    10.0.0.0 from 10.0.0.0 (10.255.0.1)
      Origin IGP, metric 0, localpref 100, IGP metric 0, weight 0, tag 0
      Received 8d17h ago, valid, internal, ECMP, ECMP contributor
      Originator: 10.255.1.2, Cluster list: 10.255.0.1
      Rx SAFI: Unicast
BGP routing table entry for 10.255.1.3/32
 Paths: 2 available
  Local
    10.0.0.128 from 10.0.0.128 (10.255.0.2)
      Origin IGP, metric 0, localpref 100, IGP metric 0, weight 0, tag 0
      Received 8d17h ago, valid, internal, ECMP head, ECMP, best, ECMP contributor
      Originator: 10.255.1.3, Cluster list: 10.255.0.2
      Rx SAFI: Unicast
  Local
    10.0.0.0 from 10.0.0.0 (10.255.0.1)
      Origin IGP, metric 0, localpref 100, IGP metric 0, weight 0, tag 0
      Received 8d17h ago, valid, internal, ECMP, ECMP contributor
      Originator: 10.255.1.3, Cluster list: 10.255.0.1
      Rx SAFI: Unicast
BGP routing table entry for 192.168.1.0/24
 Paths: 1 available
  Local
    - from - (10.255.1.1)
      Origin IGP, metric -, localpref -, IGP metric -, weight 0, tag 0
      Received 8d17h ago, valid, local, best, redistributed (Connected)
      Rx SAFI: Unicast
BGP routing table entry for 192.168.3.128/25
 Paths: 2 available
  Local
    10.0.0.0 from 10.0.0.0 (10.255.0.1)
      Origin IGP, metric 0, localpref 100, IGP metric 0, weight 0, tag 0
      Received 8d17h ago, valid, internal, ECMP head, ECMP, best, ECMP contributor
      Originator: 10.255.1.3, Cluster list: 10.255.0.1
      Rx SAFI: Unicast
  Local
    10.0.0.128 from 10.0.0.128 (10.255.0.2)
      Origin IGP, metric 0, localpref 100, IGP metric 0, weight 0, tag 0
      Received 8d17h ago, valid, internal, ECMP, ECMP contributor
      Originator: 10.255.1.3, Cluster list: 10.255.0.2
      Rx SAFI: Unicast

```
Префиксы от Spine2

```
Leaf1#show ip bgp neighbors 10.0.0.128 received-routes
BGP routing table information for VRF default
Router identifier 10.255.1.1, local AS number 65500
Route status codes: s - suppressed contributor, * - valid, > - active, E - ECMP head, e - ECMP
                    S - Stale, c - Contributing to ECMP, b - backup, L - labeled-unicast
                    % - Pending best path selection
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI Origin Validation codes: V - valid, I - invalid, U - unknown
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  AIGP       LocPref Weight  Path
 * >      10.255.0.2/32          10.0.0.128            -       -          100     -       i
 * >Ec    10.255.1.2/32          10.0.0.128            -       -          100     -       i Or-ID: 10.255.1.2 C-LST: 10.255.0.2
 * >Ec    10.255.1.3/32          10.0.0.128            -       -          100     -       i Or-ID: 10.255.1.3 C-LST: 10.255.0.2
 * >Ec    192.168.3.128/25       10.0.0.128            -       -          100     -       i Or-ID: 10.255.1.3 C-LST: 10.255.0.2


```
Прохождение PING и TRACE от хостов за Leaf3 до хостов за Leaf1

```
VPCS> ping 192.168.1.2

84 bytes from 192.168.1.2 icmp_seq=1 ttl=61 time=11.965 ms
84 bytes from 192.168.1.2 icmp_seq=2 ttl=61 time=17.757 ms
84 bytes from 192.168.1.2 icmp_seq=3 ttl=61 time=38.127 ms
84 bytes from 192.168.1.2 icmp_seq=4 ttl=61 time=22.907 ms
84 bytes from 192.168.1.2 icmp_seq=5 ttl=61 time=26.081 ms


VPCS> trace 192.168.1.2 -P 6
trace to 192.168.1.2, 8 hops max (TCP), press Ctrl+C to stop
 1   192.168.3.129   5.335 ms  3.262 ms  6.279 ms
 2   10.0.0.4   11.140 ms  8.874 ms  15.980 ms
 3   10.0.0.1   25.868 ms  15.004 ms  21.141 ms
 4   192.168.1.2   21.370 ms  19.441 ms  13.565 ms



```



 [Конфиги устройств.txt](/labs/lab04/configs)
_______________________
:fire: :star2: :alien: :boom: :metal: 
