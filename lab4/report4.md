University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction in routing](https://github.com/itmo-ict-faculty/introduction-in-routing)

Year: 2022/2023

Group: K33202

Author: Gladushko Olga

Lab: Lab4

Date of create: 16.12.2022

Date of finished: 10.01.2023

---
# Отчет по лабораторной работе №4
## "Эмуляция распределенной корпоративной сети связи, настройка iBGP, организация L3VPN, VPLS"
Автор: Гладушко Ольга

Группа: К33202

### Цель работы
Изучить протоколы BGP, MPLS и правила организации L3VPN и VPLS.

## Ход работы
Текст файла с расширением .yaml прикреплен в папке Lab4. Он был использован для развертывания тестоой сети.

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab4/Снимок.png)

Были настроены IP-адреса на интерфейсах, также были настроены OSPF и MPLS на всех роутерах. 
Далее на всех роутерах была произведена настройка iBGP с помощью команд:
```
routing bgp instance set default client-to-client-reflection=no router-id=x.x.x.x
routing bgp network add network=172.40.0.0/16
routing bgp peer add address-families=ip, l2vpn, l2vpn-cisco, vpn4 name=peer* remote-address=y.y.y.y remote-as=65530 update-source=Lo
```
Здесь x.x.x.x - адрес на виртуальном интерфейсе, y.y.y.y - адреса соседних устройств (также на виртуальном интерфейсе)

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
add export-route-targets=65530:666 import-route-targets=65530:666 interfaces=ether* route-distinguisher=65530:666 routing-mark=VRF_DEVOPS
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

### Роутер LBN (RO1.LBN)


На устройствах SGI_Prism и PC1 были выданы IP-адреса на интерфейсы. Их конфигурация:

### SGI_Prism 
```
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=192.168.10.1/24 interface=ether2 network=192.168.10.0
/ip dhcp-client 
add disabled=no interface=ether1
/system identity 
set name=SGI_Prism
```

### PC1 
```
/interface wireless security-profiles 
set [ find default=yes ] supplicant-identity=MikroTik
/ip address 
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28 
add address=192.168.10.2/24 interface=ether2 network=192.168.10.0
/ip dhcp-client 
add disabled=no interface=ether1
/system identity 
set name=PC1
```

Таблицы MPLS маршрутов:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/NY.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/LND.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/HKI.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/SPB.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/MSK.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/LBN.png)

А также проверим сеть на работоспособность пингами с SGI_Prism на PC1 и обратно:

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/ping1.png)

![.](https://github.com/OlgaGladushko/pictures/blob/main/lab3/ping2.png)

### Вывод
В ходе лабораторной работы была сделана IP/MPLS сеть связи, в которой были настроены OSPF И MPLS, а также EoMPLS. Работоспособность сети подтвердили пинги.
