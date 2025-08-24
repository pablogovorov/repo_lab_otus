## Лабораторная работа №3 по теме: "Underlay. ISIS"
___
### Цель:
Настроить ISIS для Underlay сети.

### Задание:
1. Настроите ISIS в Underlay сети, для IP связанности между всеми сетевыми устройствами.
2. Зафиксируете в документации - план работы, адресное пространство, схему сети, конфигурацию устройств
3. Убедитесь в наличии IP связанности между устройствами в ISIS домене
___

## Выполнение:

## Схема сети
![Picture background](https://github.com/pablogovorov/repo_lab_otus/blob/main/labs/lab03/mytopology.jpg)


## Адресное пространство

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
   no switchport
   ip address 10.0.0.1/31
   isis enable otus
!
interface Ethernet2
   no switchport
   ip address 10.0.0.129/31
   isis enable otus
!
interface Ethernet3
   no switchport
   ip address 192.168.1.1/24
   isis enable otus
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
   isis enable otus
!
interface Management1
!
ip routing
!
router isis otus
   net 49.0001.0000.0000.0001.00
   is-type level-2
   !
   address-family ipv4 unicast
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
   no switchport
   ip address 10.0.0.3/31
   isis enable otus
!
interface Ethernet2
   no switchport
   ip address 10.0.0.131/31
   isis enable otus
!
interface Ethernet3
   no switchport
   ip address 192.168.2.1/24
   isis enable otus
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
   isis enable otus
!
interface Management1
!
ip routing
!
router isis otus
   net 49.0001.0000.0000.0002.00
   is-type level-2
   !
   address-family ipv4 unicast
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
   no switchport
   ip address 10.0.0.5/31
   isis enable otus
!
interface Ethernet2
   no switchport
   ip address 10.0.0.133/31
   isis enable otus
!
interface Ethernet3
   no switchport
   ip address 192.168.3.1/25
   isis enable otus
!
interface Ethernet4
   no switchport
   ip address 192.168.3.129/25
   isis enable otus
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
   isis enable otus
!
interface Management1
!
ip routing
!
router isis otus
   net 49.0001.0000.0000.0003.00
   is-type level-2
   !
   address-family ipv4 unicast
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
   no switchport
   ip address 10.0.0.0/31
   isis enable otus
!
interface Ethernet2
   no switchport
   ip address 10.0.0.2/31
   isis enable otus
!
interface Ethernet3
   no switchport
   ip address 10.0.0.4/31
   isis enable otus
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
   isis enable otus
!
interface Management1
!
ip routing
!
router isis otus
   net 49.0001.0000.0000.0010.00
   is-type level-2
   !
   address-family ipv4 unicast
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
   no switchport
   ip address 10.0.0.128/31
   isis enable otus
!
interface Ethernet2
   no switchport
   ip address 10.0.0.130/31
   isis enable otus
!
interface Ethernet3
   no switchport
   ip address 10.0.0.132/31
   isis enable otus
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
   isis enable otus
!
interface Management1
!
ip routing
!
router isis otus
   net 49.0001.0000.0000.0020.00
   is-type level-2
   !
   address-family ipv4 unicast
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

Проверка IP связанности 
```
Leaf3#ping 10.255.1.1
PING 10.255.1.1 (10.255.1.1) 72(100) bytes of data.
80 bytes from 10.255.1.1: icmp_seq=1 ttl=63 time=28.9 ms
80 bytes from 10.255.1.1: icmp_seq=2 ttl=63 time=20.3 ms
80 bytes from 10.255.1.1: icmp_seq=3 ttl=63 time=16.0 ms
80 bytes from 10.255.1.1: icmp_seq=4 ttl=63 time=9.50 ms
80 bytes from 10.255.1.1: icmp_seq=5 ttl=63 time=11.8 ms

--- 10.255.1.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 75ms
rtt min/avg/max/mdev = 9.496/17.292/28.853/6.860 ms, pipe 3, ipg/ewma 18.661/22.659 ms


```
Trace от Leaf3 до Leaf1
```
Leaf3#traceroute 10.255.1.1
traceroute to 10.255.1.1 (10.255.1.1), 30 hops max, 60 byte packets
 1  10.0.0.4 (10.0.0.4)  18.503 ms  25.022 ms  29.383 ms
 2  10.255.1.1 (10.255.1.1)  58.665 ms  57.739 ms  62.889 ms

```

Проверка наличия маршрутов

```
Leaf3#show ip route isis

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

 I L2     10.0.0.0/31 [115/20]
           via 10.0.0.4, Ethernet1
 I L2     10.0.0.2/31 [115/20]
           via 10.0.0.4, Ethernet1
 I L2     10.0.0.128/31 [115/20]
           via 10.0.0.132, Ethernet2
 I L2     10.0.0.130/31 [115/20]
           via 10.0.0.132, Ethernet2
 I L2     10.255.0.1/32 [115/20]
           via 10.0.0.4, Ethernet1
 I L2     10.255.0.2/32 [115/20]
           via 10.0.0.132, Ethernet2
 I L2     10.255.1.1/32 [115/30]
           via 10.0.0.4, Ethernet1
           via 10.0.0.132, Ethernet2
 I L2     10.255.1.2/32 [115/30]
           via 10.0.0.4, Ethernet1
           via 10.0.0.132, Ethernet2
 I L2     192.168.1.0/24 [115/30]
           via 10.0.0.4, Ethernet1
           via 10.0.0.132, Ethernet2
 I L2     192.168.2.0/24 [115/30]
           via 10.0.0.4, Ethernet1
           via 10.0.0.132, Ethernet2


```

Проверка состояния базы ISIS

```
Leaf3#show isis database
Legend:
H - hostname conflict
U - node unreachable

IS-IS Instance: otus VRF: default
  IS-IS Level 2 Link State Database
    LSPID                   Seq Num  Cksum  Life Length IS  Received LSPID        Flags
    Leaf1.00-00                  90  42395   964    133 L2  0000.0000.0001.00-00  <>
    Leaf1.15-00                  75  29913   766     51 L2  0000.0000.0001.15-00  <>
    Leaf1.17-00                  82  51085   553     51 L2  0000.0000.0001.17-00  <>
    Leaf2.00-00                  79  47492   747    133 L2  0000.0000.0002.00-00  <>
    Leaf2.14-00                  76  34246   529     51 L2  0000.0000.0002.14-00  <>
    Leaf2.15-00                  77  60270   893     51 L2  0000.0000.0002.15-00  <>
    Leaf3.00-00                  85  22931   880    147 L2  0000.0000.0003.00-00  <>
    Spine1.00-00                 91  60718  1155    146 L2  0000.0000.0010.00-00  <>
    Spine1.1a-00                 75  65352   713     51 L2  0000.0000.0010.1a-00  <>
    Spine2.00-00                 82  15564   357    146 L2  0000.0000.0020.00-00  <>
    Spine2.12-00                 76  63032   893     51 L2  0000.0000.0020.12-00  <>
```
ISIS Network Topology 

```
Leaf3#show isis network topology

IS-IS Instance: otus VRF: default
  IS-IS paths to level-2 routers
    System Id        Metric   IA Metric Next-Hop         Interface                SNPA
    Leaf1            20       0         Spine1           Ethernet1                50:0:0:15:f4:e8
                                        Spine2           Ethernet2                50:0:0:72:8b:31
    Leaf2            20       0         Spine1           Ethernet1                50:0:0:15:f4:e8
                                        Spine2           Ethernet2                50:0:0:72:8b:31
    Spine1           10       0         Spine1           Ethernet1                50:0:0:15:f4:e8
    Spine2           10       0         Spine2           Ethernet2                50:0:0:72:8b:31

```
Прохождение PING и TRACE от хостов за Leaf1 до хостов за Leaf3

```
VPCS> ping 192.168.3.130

84 bytes from 192.168.3.130 icmp_seq=1 ttl=61 time=70.841 ms
84 bytes from 192.168.3.130 icmp_seq=2 ttl=61 time=22.552 ms
84 bytes from 192.168.3.130 icmp_seq=3 ttl=61 time=21.942 ms
84 bytes from 192.168.3.130 icmp_seq=4 ttl=61 time=22.253 ms
84 bytes from 192.168.3.130 icmp_seq=5 ttl=61 time=27.817 ms

VPCS> trace 192.168.3.130 -P 6
trace to 192.168.3.130, 8 hops max (TCP), press Ctrl+C to stop
 1   192.168.1.1   6.823 ms  4.068 ms  3.053 ms
 2   10.0.0.0   9.604 ms  7.852 ms  8.797 ms
 3   10.0.0.5   15.007 ms  12.332 ms  15.059 ms
 4   192.168.3.130   18.962 ms  18.218 ms  28.355 ms


```



 [Конфиги устройств.txt](/labs/lab03/configs)
_______________________
:fire: :star2: :alien: :boom: :metal: 
