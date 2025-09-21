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


## Реализация
Underlay маршрутизация на основе OSPF. Overlay на основе iBGP все spine и leaf в одной AS 65500.
Спайны работают в режиме RR.


## BGP peering между Leaf и Spine в AF l2vpn evpn
```
Leaf1#show bgp summary
BGP summary information for VRF default
Router identifier 10.255.1.1, local AS number 65500
Neighbor            AS Session State AFI/SAFI                AFI/SAFI State   NLRI Rcd   NLRI Acc
---------- ----------- ------------- ----------------------- -------------- ---------- ----------
10.255.0.1       65500 Established   IPv4 Unicast            Negotiated              0          0
10.255.0.1       65500 Established   L2VPN EVPN              Negotiated              7          7
10.255.0.2       65500 Established   IPv4 Unicast            Negotiated              0          0
10.255.0.2       65500 Established   L2VPN EVPN              Negotiated              7          7


Spine1#show bgp summary
BGP summary information for VRF default
Router identifier 10.255.0.1, local AS number 65500
Neighbor            AS Session State AFI/SAFI                AFI/SAFI State   NLRI Rcd   NLRI Acc
---------- ----------- ------------- ----------------------- -------------- ---------- ----------
10.255.1.1       65500 Established   IPv4 Unicast            Negotiated              0          0
10.255.1.1       65500 Established   L2VPN EVPN              Negotiated              1          1
10.255.1.2       65500 Established   IPv4 Unicast            Negotiated              0          0
10.255.1.2       65500 Established   L2VPN EVPN              Negotiated              2          2
10.255.1.3       65500 Established   IPv4 Unicast            Negotiated              0          0
10.255.1.3       65500 Established   L2VPN EVPN              Negotiated              2          2
```


## Наличие route-type 2 и 3 префиксов
```
Leaf1#show bgp evpn
BGP routing table information for VRF default
Router identifier 10.255.1.1, local AS number 65500
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending best path selection
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.255.1.1:10 mac-ip 0050.7966.6803
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.2:20 mac-ip 0050.7966.6807
                                 10.255.1.2            -       100     0       i Or-ID: 10.255.1.2 C-LST: 10.255.0.1
 *  ec    RD: 10.255.1.2:20 mac-ip 0050.7966.6807
                                 10.255.1.2            -       100     0       i Or-ID: 10.255.1.2 C-LST: 10.255.0.2
 * >Ec    RD: 10.255.1.3:10 mac-ip 0050.7966.6808
                                 10.255.1.3            -       100     0       i Or-ID: 10.255.1.3 C-LST: 10.255.0.1
 *  ec    RD: 10.255.1.3:10 mac-ip 0050.7966.6808
                                 10.255.1.3            -       100     0       i Or-ID: 10.255.1.3 C-LST: 10.255.0.2
 * >Ec    RD: 10.255.1.3:20 mac-ip 0050.7966.6809
                                 10.255.1.3            -       100     0       i Or-ID: 10.255.1.3 C-LST: 10.255.0.2
 *  ec    RD: 10.255.1.3:20 mac-ip 0050.7966.6809
                                 10.255.1.3            -       100     0       i Or-ID: 10.255.1.3 C-LST: 10.255.0.1
 * >      RD: 10.255.1.1:10 imet 10.255.1.1
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.2:10 imet 10.255.1.2
                                 10.255.1.2            -       100     0       i Or-ID: 10.255.1.2 C-LST: 10.255.0.2
 *  ec    RD: 10.255.1.2:10 imet 10.255.1.2
                                 10.255.1.2            -       100     0       i Or-ID: 10.255.1.2 C-LST: 10.255.0.1
 * >Ec    RD: 10.255.1.2:20 imet 10.255.1.2
                                 10.255.1.2            -       100     0       i Or-ID: 10.255.1.2 C-LST: 10.255.0.2
 *  ec    RD: 10.255.1.2:20 imet 10.255.1.2
                                 10.255.1.2            -       100     0       i Or-ID: 10.255.1.2 C-LST: 10.255.0.1
 * >Ec    RD: 10.255.1.3:10 imet 10.255.1.3
                                 10.255.1.3            -       100     0       i Or-ID: 10.255.1.3 C-LST: 10.255.0.2
 *  ec    RD: 10.255.1.3:10 imet 10.255.1.3
                                 10.255.1.3            -       100     0       i Or-ID: 10.255.1.3 C-LST: 10.255.0.1
 * >Ec    RD: 10.255.1.3:20 imet 10.255.1.3
                                 10.255.1.3            -       100     0       i Or-ID: 10.255.1.3 C-LST: 10.255.0.2
 *  ec    RD: 10.255.1.3:20 imet 10.255.1.3
                                 10.255.1.3            -       100     0       i Or-ID: 10.255.1.3 C-LST: 10.255.0.1
```
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
vlan 10
   name vlan10
!
interface Ethernet1
   no switchport
   ip address 10.0.0.1/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   no switchport
   ip address 10.0.0.129/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
   switchport access vlan 10
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
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vlan10
!
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
!
ip routing
!
router bgp 65500
   neighbor 10.255.0.1 remote-as 65500
   neighbor 10.255.0.1 update-source Loopback0
   neighbor 10.255.0.1 send-community extended
   neighbor 10.255.0.2 remote-as 65500
   neighbor 10.255.0.2 update-source Loopback0
   neighbor 10.255.0.2 send-community extended
   !
   vlan 10
      rd auto
      route-target both 65500:10010
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.0.1 activate
      neighbor 10.255.0.2 activate
!
router multicast
   ipv4
      software-forwarding kernel
   !
   ipv6
      software-forwarding kernel
!
router ospf 1
   router-id 10.255.1.1
   max-lsa 12000
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
vlan 10
   name vlan10
!
vlan 20
   name vlan20
!
interface Ethernet1
   no switchport
   ip address 10.0.0.3/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   no switchport
   ip address 10.0.0.131/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
   switchport access vlan 20
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
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vlan10
!
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
!
ip routing
!
router bgp 65500
   neighbor 10.255.0.1 remote-as 65500
   neighbor 10.255.0.1 update-source Loopback0
   neighbor 10.255.0.1 send-community extended
   neighbor 10.255.0.2 remote-as 65500
   neighbor 10.255.0.2 update-source Loopback0
   neighbor 10.255.0.2 send-community extended
   !
   vlan 10
      rd auto
      route-target both 65500:10010
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 65500:10020
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.0.1 activate
      neighbor 10.255.0.2 activate
!
router multicast
   ipv4
      software-forwarding kernel
   !
   ipv6
      software-forwarding kernel
!
router ospf 1
   router-id 10.255.1.2
   max-lsa 12000
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
vlan 10
   name vlan10
!
vlan 20
   name vlan20
!
interface Ethernet1
   no switchport
   ip address 10.0.0.5/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   no switchport
   ip address 10.0.0.133/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
   switchport access vlan 10
!
interface Ethernet4
   switchport access vlan 20
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
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
!
ip routing
!
router bgp 65500
   neighbor 10.255.0.1 remote-as 65500
   neighbor 10.255.0.1 update-source Loopback0
   neighbor 10.255.0.1 send-community extended
   neighbor 10.255.0.2 remote-as 65500
   neighbor 10.255.0.2 update-source Loopback0
   neighbor 10.255.0.2 send-community extended
   !
   vlan 10
      rd auto
      route-target both 65500:10010
      redistribute learned
   !
   vlan 20
      rd auto
      route-target both 65500:10020
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.0.1 activate
      neighbor 10.255.0.2 activate
!
router multicast
   ipv4
      software-forwarding kernel
   !
   ipv6
      software-forwarding kernel
!
router ospf 1
   router-id 10.255.1.3
   max-lsa 12000
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
   no switchport
   ip address 10.0.0.0/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   no switchport
   ip address 10.0.0.2/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
   no switchport
   ip address 10.0.0.4/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
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
   ip ospf area 0.0.0.0
!
interface Management1
!
ip routing
!
router bgp 65500
   neighbor 10.255.1.1 remote-as 65500
   neighbor 10.255.1.1 update-source Loopback0
   neighbor 10.255.1.1 route-reflector-client
   neighbor 10.255.1.1 send-community extended
   neighbor 10.255.1.2 remote-as 65500
   neighbor 10.255.1.2 update-source Loopback0
   neighbor 10.255.1.2 route-reflector-client
   neighbor 10.255.1.2 send-community extended
   neighbor 10.255.1.3 remote-as 65500
   neighbor 10.255.1.3 update-source Loopback0
   neighbor 10.255.1.3 route-reflector-client
   neighbor 10.255.1.3 send-community extended
   !
   address-family evpn
      neighbor 10.255.1.1 activate
      neighbor 10.255.1.2 activate
      neighbor 10.255.1.3 activate
!
router multicast
   ipv4
      software-forwarding kernel
   !
   ipv6
      software-forwarding kernel
!
router ospf 1
   router-id 10.255.0.1
   max-lsa 12000
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
   no switchport
   ip address 10.0.0.128/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet2
   no switchport
   ip address 10.0.0.130/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
!
interface Ethernet3
   no switchport
   ip address 10.0.0.132/31
   ip ospf network point-to-point
   ip ospf area 0.0.0.0
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
   ip ospf area 0.0.0.0
!
interface Management1
!
ip routing
!
router bgp 65500
   neighbor 10.255.1.1 remote-as 65500
   neighbor 10.255.1.1 update-source Loopback0
   neighbor 10.255.1.1 route-reflector-client
   neighbor 10.255.1.1 send-community extended
   neighbor 10.255.1.2 remote-as 65500
   neighbor 10.255.1.2 update-source Loopback0
   neighbor 10.255.1.2 route-reflector-client
   neighbor 10.255.1.2 send-community extended
   neighbor 10.255.1.3 remote-as 65500
   neighbor 10.255.1.3 update-source Loopback0
   neighbor 10.255.1.3 route-reflector-client
   neighbor 10.255.1.3 send-community extended
   !
   address-family evpn
      neighbor 10.255.1.1 activate
      neighbor 10.255.1.2 activate
      neighbor 10.255.1.3 activate
!
router multicast
   ipv4
      software-forwarding kernel
   !
   ipv6
      software-forwarding kernel
!
router ospf 1
   router-id 10.255.0.2
   max-lsa 12000
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
