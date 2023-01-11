University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)

Year: 2022/2023

Group: K33202

Author: Gladushko Olga

Lab: Lab4

Date of create: 16.12.2022

Date of finished: 13.01.2023

---
# Отчет по лабораторной работе №4
## "Эмуляция распределенной корпоративной сети связи, настройка iBGP, организация L3VPN, VPLS"
Автор: Гладушко Ольга

Группа: К33202

### Цель работы
Изучить протоколы BGP, MPLS и правила организации L3VPN и VPLS.

## Ход работы
## Часть 1

Схема связи сети:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab4/Часть1.drawio.png)

Текст файла с расширением .yaml прикреплен в папке Lab4. Он был использован для развертывания тестоой сети.

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab4/Снимок.png)

Были настроены IP-адреса на интерфейсах, также были настроены OSPF и MPLS на всех роутерах. 
Далее на всех роутерах была произведена настройка iBGP с помощью команд:
```
routing bgp instance set default client-to-client-reflection=no router-id=x.x.x.x
routing bgp network add network=172.40.0.0/16
routing bgp peer add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer* remote-address=y.y.y.y remote-as=65530 update-source=Lo
```
Здесь x.x.x.x - адрес на виртуальном интерфейсе, y.y.y.y - адреса соседних сетевых устройств (также на виртуальном интерфейсе).

На трех роутерах NY, SPB и SVL был настроен VRF:
```
ip route vrf add export-route-targets=65530:666 import-route-targets=65530:666 interfaces=ether* route-distinguisher=65530:666 routing-mark=VRF_DEVOPS
routing bgp instance vrf add redistribute-connected=yes routing-mark=VRF_DEVOPS
```
Здесь ether* - интерфейс во "внутреннюю" сеть.

Далее будут представлены конфигурации всех устройств:

### Роутер NY (RO1.NY)
```
/interface bridge
add name Lo
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance 
set default client-to-client-reflection=no router-id=10.0.10.1
/routing ospf instance
set [ find default=yes ] router-id=10.0.10.1
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.0.10.1/32 interface=Lo
add address=172.40.40.2/30 interface=ether3
add address=192.168.66.1/30 interface=ether2
/ip dhcp-client 
add disabled=no interface=ether1
/ip route vrf 
add export-route-targets=65530:666 import-route-targets=65530:666 interfaces=ether2 route-distinguisher=65530:666 routing-mark=VRF_DEVOPS
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether3
/routing bgp instance vrf 
add redistribute-connected=yes routing-mark=VRF_DEVOPS
/routing bgp network 
add network=172.40.0.0/16
/routing bgp peer 
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.11.1 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity 
set name=RO1.NY
```

### Роутер LND (RO1.LND)
```
/interface bridge
add name Lo
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance 
set default router-id=10.0.11.1
/routing ospf instance
set [ find default=yes ] router-id=10.0.11.1
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.0.11.1/32 interface=Lo
add address=172.40.40.1/30 interface=ether2
add address=172.40.40.5/30 interface=ether3
add address=172.40.40.17/30 interface=ether4
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing bgp network 
add network=172.40.0.0/16
/routing bgp peer 
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.12.1 remote-as=65530 route-reflect=yes update-source=Lo
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer2 remote-address=10.0.10.1 remote-as=65530 update-source=Lo
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.14.1 remote-as=65530 route-reflect=yes update-source=Lo
/routing ospf network
add area=backbone
/system identity 
set name=RO1.LND
```

### Роутер LBN (RO1.LBN)
```
/interface bridge
add name Lo
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance 
set default router-id=10.0.12.1
/routing ospf instance
set [ find default=yes ] router-id=10.0.12.1
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.0.12.1/32 interface=Lo
add address=172.40.40.6/30 interface=ether2
add address=172.40.40.9/30 interface=ether3
add address=172.40.40.13/30 interface=ether4
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing bgp network 
add network=172.40.0.0/16
/routing bgp peer 
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.11.1 remote-as=65530 route-reflect=yes update-source=Lo
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer2 remote-address=10.0.13.1 remote-as=65530 update-source=Lo
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.14.1 remote-as=65530 route-reflect=yes update-source=Lo
/routing ospf network
add area=backbone
/system identity 
set name=RO1.LBN
```

### Роутер SVL (RO1.SVL)
```
/interface bridge
add name Lo
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance 
set default client-to-client-reflection=no router-id=10.0.13.1
/routing ospf instance
set [ find default=yes ] router-id=10.0.13.1
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.0.13.1/32 interface=Lo
add address=172.40.40.10/30 interface=ether2
add address=192.168.66.9/30 interface=ether3
/ip dhcp-client 
add disabled=no interface=ether1
/ip route vrf 
add export-route-targets=65530:666 import-route-targets=65530:666 interfaces=ether3 route-distinguisher=65530:666 routing-mark=VRF_DEVOPS
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
/routing bgp instance vrf 
add redistribute-connected=yes routing-mark=VRF_DEVOPS
/routing bgp network 
add network=172.40.0.0/16
/routing bgp peer 
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.12.1 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity 
set name=RO1.SVL
```

### Роутер HKI (RO1.HKI)
```
/interface bridge
add name Lo
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance 
set default router-id=10.0.14.1
/routing ospf instance
set [ find default=yes ] router-id=10.0.14.1
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.0.14.1/32 interface=Lo
add address=172.40.40.18/30 interface=ether2
add address=172.40.40.14/30 interface=ether3
add address=172.40.40.21/30 interface=ether4
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
add interface=ether3
add interface=ether4
/routing bgp network 
add network=172.40.0.0/16
/routing bgp peer 
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.11.1 remote-as=65530 route-reflect=yes update-source=Lo
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer2 remote-address=10.0.15.1 remote-as=65530 update-source=Lo
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.12.1 remote-as=65530 route-reflect=yes update-source=Lo
/routing ospf network
add area=backbone
/system identity 
set name=RO1.HKI
```

### Роутер SPB (RO1.SPB)
```
/interface bridge
add name Lo
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance 
set default client-to-client-reflection=no router-id=10.0.15.1
/routing ospf instance
set [ find default=yes ] router-id=10.0.15.1
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.0.15.1/32 interface=Lo
add address=172.40.40.22/30 interface=ether2
add address=192.168.66.21/30 interface=ether3
/ip dhcp-client 
add disabled=no interface=ether1
/ip route vrf 
add export-route-targets=65530:666 import-route-targets=65530:666 interfaces=ether3 route-distinguisher=65530:666 routing-mark=VRF_DEVOPS
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
/routing bgp instance vrf 
add redistribute-connected=yes routing-mark=VRF_DEVOPS
/routing bgp network 
add network=172.40.0.0/16
/routing bgp peer 
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.14.1 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity 
set name=RO1.SPB
```
Проверяем VRF:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab4/ping.png)

## Часть 2


Схема связи сети:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab4/Часть2.drawio.png)

После выполнения первой части работы VRF был разобран на трех роутерах (NY, SPB и SVL), а далее на них был собран VPLS c помощью команд:
```
interface bridge add name=VPLS
interface vpls add disabled=no name=VPLS* remote-peer=x.x.x.x vpls-id=15:0
interface bridge port add bridge=VPLS interface=ether*
interface bridge port add bridge=VPLS interface=VPLS*
```
Здесь x.x.x.x - адреса двух роутеров (на виртуальных интерфейсах), а ether* - интерфейс, выходящий на PC.

Далее будут представлены измененные конфигурации трех устройств:

### Роутер NY (RO1.NY)
```
/interface bridge
add name Lo
add name=VPLS
/interface vpls 
add disabled=no l2mtu=1500 mac-address=02:4C:87:65:E5:C1 name=VPLS1 remote-peer=10.0.15.1 vpls-id=15:0
add disabled=no l2mtu=1500 mac-address=02:25:1E:8F:C4:76 name=VPLS3 remote-peer=10.0.13.1 vpls-id=15:0
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance 
set default client-to-client-reflection=no router-id=10.0.10.1
/routing ospf instance
set [ find default=yes ] router-id=10.0.10.1
/interface bridge port 
add bridge=VPLS interface=VPLS1
add bridge=VPLS interface=VPLS3
add bridge=VPLS interface=ether2
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.0.10.1/32 interface=Lo
add address=172.40.40.2/30 interface=ether3
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether3
/routing bgp network 
add network=172.40.0.0/16
/routing bgp peer 
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.11.1 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity 
set name=RO1.NY
```

### Роутер SVL (RO1.SVL)
```
/interface bridge
add name Lo
add name=VPLS
/interface vpls 
add disabled=no l2mtu=1500 mac-address=02:64:F7:0E:2F:61 name=VPLS2 remote-peer=10.0.15.1 vpls-id=15:0
add disabled=no l2mtu=1500 mac-address=02:14:3F:01:21:93 name=VPLS3 remote-peer=10.0.10.1 vpls-id=15:0
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance 
set default client-to-client-reflection=no router-id=10.0.13.1
/routing ospf instance
set [ find default=yes ] router-id=10.0.13.1
/interface bridge port 
add bridge=VPLS interface=VPLS2
add bridge=VPLS interface=VPLS3
add bridge=VPLS interface=ether3
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.0.13.1/32 interface=Lo
add address=172.40.40.10/30 interface=ether2
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
/routing bgp network 
add network=172.40.0.0/16
/routing bgp peer 
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.12.1 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity 
set name=RO1.SVL
```

### Роутер SPB (RO1.SPB)
```
/interface bridge
add name Lo
add name VPLS
/interface vpls 
add disabled=no l2mtu=1500 mac-address=02:92:7A:5E:B8:F9 name=VPLS1 remote-peer=10.0.10.1 vpls-id=15:0
add disabled=no l2mtu=1500 mac-address=02:B0:EB:40:27:DF name=VPLS2 remote-peer=10.0.13.1 vpls-id=15:0
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/routing bgp instance 
set default client-to-client-reflection=no router-id=10.0.15.1
/routing ospf instance
set [ find default=yes ] router-id=10.0.15.1
/interface bridge port 
add bridge=VPLS interface=VPLS1
add bridge=VPLS interface=VPLS2
add bridge=VPLS interface=ether3
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=10.0.15.1/32 interface=Lo
add address=172.40.40.22/30 interface=ether2
/ip dhcp-client 
add disabled=no interface=ether1
/mpls ldp
set enabled=yes
/mpls ldp interface
add interface=ether2
/routing bgp network 
add network=172.40.0.0/16
/routing bgp peer 
add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer1 remote-address=10.0.14.1 remote-as=65530 update-source=Lo
/routing ospf network
add area=backbone
/system identity 
set name=RO1.SPB
```

На PC были выданы IP-адреса на интерфейсы.
Их конфигурация:

### PC1
```
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=172.10.10.1/24 interface=ether2
/ip dhcp-client 
add disabled=no interface=ether1
/system identity 
set name=PC1
```

### PC2
```
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=172.10.10.2/24 interface=ether2
/ip dhcp-client 
add disabled=no interface=ether1
/system identity 
set name=PC2
```

### PC3
```
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=172.10.10.3/24 interface=ether2
/ip dhcp-client 
add disabled=no interface=ether1
/system identity 
set name=PC3
```

Проверим связность:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab4/ping3.png)

## Вывод
В ходе лабораторной работы была сделана IP/MPLS сеть связи, в которой были настроены BGP И VPLS (помимо этого также были настроены OSPF и MPLS). Работоспособность сети подтвердили пинги и в первой, и во второй части работы.
