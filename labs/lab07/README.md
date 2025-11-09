## Лабораторная работа №7 по теме: "VXLAN. Multihoming"
___
### Цель:
Настроить отказоустойчивое подключение клиентов с использованием EVPN Multihoming.

### Задание:
1. Подключение клиента 2-мя линками к различным Leaf.
2. Настроика агрегированного канала со стороны клиента.
3. Настройка multihoming для работы в Overlay сети.  ESI LAG.
4. Зафиксируете в документации - план работы, адресное пространство, схему сети, конфигурацию устройств.
5. Тестирование на отказоустойчивость при отключении одного из линков.
___

## Выполнение:

## Схема сети
![Picture background](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab07/jpg/mytopology.jpg)


## Реализация
Underlay маршрутизация на основе OSPF. Overlay на основе iBGP все spine и leaf в одной AS 65500.
Спайны работают в режиме RR.

Со стороны multih-sw собран po100 (eth1+eth2) c ESI-LAG Leaf1+Leaf2




| Host  | VNI  | VLAN  |
| ------------ | ------------ |------------ |
| 192.168.1.2   | VNI 10010 | Vlan 10 |
| 192.168.1.3   | VNI 10010 | Vlan 10 |

**Показать информацию о BGP EVPN instance:**

```bash
Leaf1#show bgp evpn instance
EVPN instance: VLAN 10
  Route distinguisher: 10.255.1.1:10
  Route target import: Route-Target-AS:65500:10010
  Route target export: Route-Target-AS:65500:10010
  Service interface: VLAN-based
  Local VXLAN IP address: 10.255.1.1
  VXLAN: enabled
  MPLS: disabled
  Local ethernet segment:
    ESI: 0000:0000:0000:0000:0001
      Type: 0 (administratively configured)
      Interface: Port-Channel100
      Mode: all-active
      State: up
      ES-Import RT: 00:00:00:00:00:01
      DF election algorithm: preference
      Designated forwarder: 10.255.1.1
      Non-Designated forwarder: 10.255.1.2
```

 
**ping от 192.168.1.2 до 192.168.1.3:**
```bash
VPCS> ping 192.168.1.3

84 bytes from 192.168.1.3 icmp_seq=1 ttl=64 time=63.171 ms
84 bytes from 192.168.1.3 icmp_seq=2 ttl=64 time=24.103 ms
84 bytes from 192.168.1.3 icmp_seq=3 ttl=64 time=22.191 ms
84 bytes from 192.168.1.3 icmp_seq=4 ttl=64 time=22.909 ms
84 bytes from 192.168.1.3 icmp_seq=5 ttl=64 time=22.402 ms
```

**Проверка пинга при обрыве одного из линков на Leaf1 или Leaf2**

Пинг идёт через Leaf1:
[![](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab07/jpg/pingl1.jpg)](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab07/jpg/pingl1.jpg)  

после обрыва линка пинг проходит через Leaf2  

[![](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab07/jpg/pingl2.jpg)](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab07/jpg/pingl2.jpg)

**Проверка EVPN маршрутов Type-1 для данного ESI**

```bash
Leaf1#show bgp evpn route-type auto-discovery
BGP routing table information for VRF default
Router identifier 10.255.1.1, local AS number 65500
Route status codes: * - valid, > - active, S - Stale, E - ECMP head, e - ECMP
                    c - Contributing to ECMP, % - Pending best path selection
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >      RD: 10.255.1.1:10 auto-discovery 0 0000:0000:0000:0000:0001
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.2:10 auto-discovery 0 0000:0000:0000:0000:0001
                                 10.255.1.2            -       100     0       i Or-ID: 10.255.1.2 C-LST: 10.255.0.1
 *  ec    RD: 10.255.1.2:10 auto-discovery 0 0000:0000:0000:0000:0001
                                 10.255.1.2            -       100     0       i Or-ID: 10.255.1.2 C-LST: 10.255.0.2
 * >      RD: 10.255.1.1:1 auto-discovery 0000:0000:0000:0000:0001
                                 -                     -       -       0       i
 * >Ec    RD: 10.255.1.2:1 auto-discovery 0000:0000:0000:0000:0001
                                 10.255.1.2            -       100     0       i Or-ID: 10.255.1.2 C-LST: 10.255.0.1
 *  ec    RD: 10.255.1.2:1 auto-discovery 0000:0000:0000:0000:0001
                                 10.255.1.2            -       100     0       i Or-ID: 10.255.1.2 C-LST: 10.255.0.2

```

**Проверка Designated Forwarder (DF)**


```bash
Leaf2#show bgp evpn esi 0000:0000:0000:0000:0001 detail
BGP routing table information for VRF default
Router identifier 10.255.1.2, local AS number 65500
BGP routing table entry for auto-discovery 0 0000:0000:0000:0000:0001, Route Distinguisher: 10.255.1.1:10
 Paths: 2 available
  Local
    10.255.1.1 from 10.255.0.2 (10.255.0.2)
      Origin IGP, metric -, localpref 100, weight 0, tag 0, valid, internal, ECMP head, ECMP, best, ECMP contributor
      Originator: 10.255.1.1, Cluster list: 10.255.0.2
      Extended Community: Route-Target-AS:65500:10010 TunnelEncap:tunnelTypeVxlan
      VNI: 10010
  Local
    10.255.1.1 from 10.255.0.1 (10.255.0.1)
      Origin IGP, metric -, localpref 100, weight 0, tag 0, valid, internal, ECMP, ECMP contributor
      Originator: 10.255.1.1, Cluster list: 10.255.0.1
      Extended Community: Route-Target-AS:65500:10010 TunnelEncap:tunnelTypeVxlan
      VNI: 10010
BGP routing table entry for auto-discovery 0 0000:0000:0000:0000:0001, Route Distinguisher: 10.255.1.2:10
 Paths: 1 available
  Local
    - from - (0.0.0.0)
      Origin IGP, metric -, localpref -, weight 0, tag 0, valid, local, best
      Extended Community: Route-Target-AS:65500:10010 TunnelEncap:tunnelTypeVxlan
      VNI: 10010
BGP routing table entry for auto-discovery 0000:0000:0000:0000:0001, Route Distinguisher: 10.255.1.1:1
 Paths: 2 available, Priority: high
  Local
    10.255.1.1 from 10.255.0.1 (10.255.0.1)
      Origin IGP, metric -, localpref 100, weight 0, tag 0, valid, internal, ECMP head, ECMP, best, ECMP contributor
      Originator: 10.255.1.1, Cluster list: 10.255.0.1
      Extended Community: Route-Target-AS:65500:10010 TunnelEncap:tunnelTypeVxlan EvpnEsiLabel:0
      VNI: 0
  Local
    10.255.1.1 from 10.255.0.2 (10.255.0.2)
      Origin IGP, metric -, localpref 100, weight 0, tag 0, valid, internal, ECMP, ECMP contributor
      Originator: 10.255.1.1, Cluster list: 10.255.0.2
      Extended Community: Route-Target-AS:65500:10010 TunnelEncap:tunnelTypeVxlan EvpnEsiLabel:0
      VNI: 0
BGP routing table entry for auto-discovery 0000:0000:0000:0000:0001, Route Distinguisher: 10.255.1.2:1
 Paths: 1 available, Priority: high
  Local
    - from - (0.0.0.0)
      Origin IGP, metric -, localpref -, weight 0, tag 0, valid, local, best
      Extended Community: Route-Target-AS:65500:10010 TunnelEncap:tunnelTypeVxlan EvpnEsiLabel:0
BGP routing table entry for ethernet-segment 0000:0000:0000:0000:0001 10.255.1.1, Route Distinguisher: 10.255.1.1:1
 Paths: 2 available
  Local
    10.255.1.1 from 10.255.0.1 (10.255.0.1)
      Origin IGP, metric -, localpref 100, weight 0, tag 0, valid, internal, ECMP head, ECMP, best, ECMP contributor
      Originator: 10.255.1.1, Cluster list: 10.255.0.1
      Extended Community: TunnelEncap:tunnelTypeVxlan EvpnEsImportRt:00:00:00:00:00:01 DF Election: Preference 200
  Local
    10.255.1.1 from 10.255.0.2 (10.255.0.2)
      Origin IGP, metric -, localpref 100, weight 0, tag 0, valid, internal, ECMP, ECMP contributor
      Originator: 10.255.1.1, Cluster list: 10.255.0.2
      Extended Community: TunnelEncap:tunnelTypeVxlan EvpnEsImportRt:00:00:00:00:00:01 DF Election: Preference 200
BGP routing table entry for ethernet-segment 0000:0000:0000:0000:0001 10.255.1.2, Route Distinguisher: 10.255.1.2:1
 Paths: 1 available
  Local
    - from - (0.0.0.0)
      Origin IGP, metric -, localpref -, weight 0, tag 0, valid, local, best
      Extended Community: TunnelEncap:tunnelTypeVxlan EvpnEsImportRt:00:00:00:00:00:01 DF Election: Preference 100


```



-----------------------------

## Конфигурация устройств


<details> 

<summary> Leaf1 </summary>

```
! Command: show running-config
! device: Leaf1 (vEOS-lab, EOS-4.33.1F)
!
! boot system flash:/vEOS-lab.swi
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
interface Port-Channel100
   switchport trunk allowed vlan 10
   switchport mode trunk
   !
   evpn ethernet-segment
      identifier 0000:0000:0000:0000:0001
      designated-forwarder election algorithm preference 200
      route-target import 00:00:00:00:00:01
   lacp system-id 0000.0000.0001
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
   switchport mode trunk
   channel-group 100 mode active
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
! Command: show running-config
! device: Leaf2 (vEOS-lab, EOS-4.33.1F)
!
! boot system flash:/vEOS-lab.swi
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
interface Port-Channel100
   description "ESI-LAG to Server"
   switchport trunk allowed vlan 10
   switchport mode trunk
   !
   evpn ethernet-segment
      identifier 0000:0000:0000:0000:0001
      designated-forwarder election algorithm preference 100
      route-target import 00:00:00:00:00:01
   lacp system-id 0000.0000.0001
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
   description "To Server"
   switchport mode trunk
   channel-group 100 mode active
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
   router-id 10.255.1.2
   max-lsa 12000
!
end


```
</details>
<details> 

<summary> Leaf3 </summary>

```
! Command: show running-config
! device: Leaf3 (vEOS-lab, EOS-4.33.1F)
!
! boot system flash:/vEOS-lab.swi
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
   router-id 10.255.1.3
   max-lsa 12000
!
end



```
</details>

<details> 
<summary> multih-sw </summary>

```

! Command: show running-config
! device: multih-sw (vEOS-lab, EOS-4.33.1F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
no service interface inactive port-id allocation disabled
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname multih-sw
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
interface Port-Channel100
   description "Server to ESI-LAG"
   switchport mode trunk
!
interface Ethernet1
   description "to esi-sw"
   switchport mode trunk
   channel-group 100 mode active
!
interface Ethernet2
   description "to esi-sw"
   switchport mode trunk
   channel-group 100 mode active
!
interface Ethernet3
   switchport access vlan 10
!
interface Ethernet4
!
interface Management1
!
no ip routing
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
! Command: show running-config
! device: Spine1 (vEOS-lab, EOS-4.33.1F)
!
! boot system flash:/vEOS-lab.swi
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
! Command: show running-config
! device: Spine2 (vEOS-lab, EOS-4.33.1F)
!
! boot system flash:/vEOS-lab.swi
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





 [Конфиги устройств.txt](/labs/lab07/configs)
_______________________
🔨 👀 🐶 🗺️ 🚀
