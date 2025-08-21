## Лабораторная работа №2 по теме: "Underlay. OSPF"
___
### Цель:
Настроить OSPF для Underlay сети.

### Задание:
1. Настроите OSPF в Underlay сети, для IP связанности между всеми сетевыми устройствами.
2. Зафиксируете в документации - план работы, адресное пространство, схему сети, конфигурацию устройств
3. Убедитесь в наличии IP связанности между устройствами в OSFP домене
___

## Выполнение:

## Схема сети
![Picture background](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab02/mytopology.jpg)


## Адресное пространство

| Loopback0 | adrss | 
------ | ------ |
Пул: | 10.255.0.0/20 |
SP1: | 10.255.0.1/32 | 
SP2: | 10.255.0.2/32 |
LE1: | 10.255.1.1/32 |
LE2: |10.255.1.2/32 |
LE3: |10.255.1.3/32 |

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
!
interface Management1
!
ip routing
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
!
interface Management1
!
ip routing
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
!
interface Management1
!
ip routing
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
__________________________-

Проверка IP связанности 
```
Leaf1#ping 10.0.0.133
PING 10.0.0.133 (10.0.0.133) 72(100) bytes of data.
80 bytes from 10.0.0.133: icmp_seq=1 ttl=63 time=9.80 ms
80 bytes from 10.0.0.133: icmp_seq=2 ttl=63 time=5.42 ms
80 bytes from 10.0.0.133: icmp_seq=3 ttl=63 time=6.86 ms
80 bytes from 10.0.0.133: icmp_seq=4 ttl=63 time=7.15 ms
80 bytes from 10.0.0.133: icmp_seq=5 ttl=63 time=7.77 ms

--- 10.0.0.133 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 51ms
rtt min/avg/max/mdev = 5.417/7.400/9.804/1.428 ms, ipg/ewma 12.803/8.610 ms

```
Проверка наличия маршрутов
```
Leaf1#sho ip route ospf

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

 O        10.0.0.2/31 [110/20]
           via 10.0.0.0, Ethernet1
 O        10.0.0.4/31 [110/20]
           via 10.0.0.0, Ethernet1
 O        10.0.0.130/31 [110/20]
           via 10.0.0.128, Ethernet2
 O        10.0.0.132/31 [110/20]
           via 10.0.0.128, Ethernet2

```

Проверка состояния базы OSPF
```
Leaf1#show ip ospf database

            OSPF Router with ID(10.255.1.1) (Instance ID 1) (VRF default)


                 Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age         Seq#         Checksum Link count
10.255.0.1      10.255.0.1      1090        0x80000092   0xbdda   6
10.255.0.2      10.255.0.2      1022        0x8000000b   0x3bdf   6
10.255.1.2      10.255.1.2      846         0x80000007   0xcca9   4
10.255.1.3      10.255.1.3      332         0x80000007   0xadbe   4
10.255.1.1      10.255.1.1      998         0x80000094   0xd022   4
```



 [Конфиги устройств.txt](/labs/lab02/configs)
_______________________
:metal: :lips: :heart: :boom: :rocket: 
