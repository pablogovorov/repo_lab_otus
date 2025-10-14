## Лабораторная работа №6 по теме: "VxLAN. EVPN L3"
___
### Цель:
Настроить маршрутизацию в рамках Overlay между клиентами.

### Задание:
1. Настроите каждого клиента в своем VNI.
2. Настроите маршрутизацию между клиентами/
3. Зафиксируете в документации - план работы, адресное пространство, схему сети, конфигурацию устройств/
___

## Выполнение:

## Схема сети
![Picture background](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab06/jpg/mytopology.jpg)


## Реализация
Underlay маршрутизация на основе OSPF. Overlay на основе iBGP все spine и leaf в одной AS 65500.
Спайны работают в режиме RR.



| Host  | VNI  | VLAN  |
| ------------ | ------------ |------------ |
| 192.168.1.2   | VNI 10010 | Vlan 10 |
| 192.168.2.2   | VNI 10020 | Vlan 20 |
| 192.168.3.2   | VNI 777  | Vlan 30|
| 192.168.4.2   | VNI 777  | Vlan 40|


__Assymmetric IRB настроен между клиентами:__  
192.168.1.2 (VNI 10010, VL10) и 192.168.2.2 (VNI 10020,VL20)  
VPCS> ping 192.168.2.2  

84 bytes from 192.168.2.2 icmp_seq=1 ttl=63 time=177.439 ms  
84 bytes from 192.168.2.2 icmp_seq=2 ttl=63 time=15.971 ms  
...
[![](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab06/jpg/assym10010.jpg)](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab06/jpg/assym10010.jpg)
[![](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab06/jpg/assym10020.jpg)](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab06/jpg/assym10020.jpg)



__Symmetric IRB настроен между клиентам:__  
192.168.3.2 (VNI777, VL30) и 192.168.4.2 (VNI777, VL40)  
VPCS> ping 192.168.4.2  

84 bytes from 192.168.4.2 icmp_seq=1 ttl=62 time=59.886 ms  
84 bytes from 192.168.4.2 icmp_seq=2 ttl=62 time=21.326 ms  
...
[![](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab06/jpg/symm777req.jpg)](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab06/jpg/symm777req.jpg)
[![](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab06/jpg/sym777rep.jpg)](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab06/jpg/sym777rep.jpg)

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
vlan 20
   name vlan20
!
vlan 30
   name vlan30
!
vrf instance VRF-IRB1
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
   switchport access vlan 30
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
   ip address 192.168.1.1/24
!
interface Vlan20
   ip address 192.168.2.1/24
!
interface Vlan30
   vrf VRF-IRB1
   ip address virtual 192.168.3.1/24
!
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
   vxlan vrf VRF-IRB1 vni 777
!
ip routing
ip routing vrf VRF-IRB1
!
router bgp 65500
   router-id 10.255.1.1
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
   vlan 30
      rd auto
      route-target both 65500:10030
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.0.1 activate
      neighbor 10.255.0.2 activate
   !
   vrf VRF-IRB1
      rd 10.255.1.1:777
      route-target import evpn 65500:777
      route-target export evpn 65500:777
      network 192.168.3.0/24
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
   ip address 192.168.1.1/24
!
interface Vlan20
   ip address 192.168.2.1/24
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
vlan 40
   name vlan40
!
vrf instance VRF-IRB1
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
   switchport access vlan 40
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
interface Vlan10
   ip address 192.168.1.1/24
!
interface Vlan20
   ip address 192.168.2.1/24
!
interface Vlan40
   vrf VRF-IRB1
   ip address virtual 192.168.4.1/24
!
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
   vxlan vrf VRF-IRB1 vni 777
!
ip routing
ip routing vrf VRF-IRB1
!
router bgp 65500
   router-id 10.255.1.3
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
   vlan 40
      rd auto
      route-target both 65500:10040
      redistribute learned
   !
   address-family evpn
      neighbor 10.255.0.1 activate
      neighbor 10.255.0.2 activate
   !
   vrf VRF-IRB1
      rd 10.255.1.3:777
      route-target import evpn 65500:777
      route-target export evpn 65500:777
      network 192.168.4.0/24
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





 [Конфиги устройств.txt](/labs/lab06/configs)
_______________________
:musical_note: :heartbeat: :eyes: :saruman: :runner: 
